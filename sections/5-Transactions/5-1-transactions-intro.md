# Chapter 5. Transactions

Unlike many other blockchains where there are 1 (Bitcoin) or 2 (Ethereum) types of transactions, Waves at the time of this writing, there are 17 of them. Below is a diagram with a conditional division of all current types of transactions into categories:

![Waves transaction types](../../assets/5-1-1-transaction-types.png "Waves Transaction Types")

You may already have questions: "Why do transactions have such a chaotic numbering order? Why does the numbering not go sequentially within at least one category?"

The fact is that transactions received numbers (aka IDs) as they were added to the protocol. In this part, we will consider transactions in the same order, that is, as they appear on the blockchain.

**Important: Many types of transactions have multiple versions, in this book we will look at the latest current versions on the Mainnet.**

Working with transactions is carried out using the node's API, which allows you to both receive information about transactions and send them. For you as a developer, a transaction in most cases will look like plain JSON:

``` 'json
{
  "senderPublicKey": "CRxqEuxhdZBEHX42MU4FfyJxuHmbDBTaHMhM3Uki7pLw",
  "amount": 1000000000, "signature": "4W9nWkfRm62rTQiuZX6bowWmDnz5n3cKhCZmLcYgivK53mBt3TzH6Z52fV6fXPSZn5bZc97rNo76usnNEoQcTHaq",
  "fee": 100000,
  "type": 4,
  "version": 1,
  "attachment": "",
  "sender": "3NBVqYXrapgJP9atQccdBPAgJPwHDKkh6A8",
  "feeAssetId": null,
  "proofs": ["4W9nWkfRm62rTQiuZX6bowWmDnz5n3cKhCZmLcYgivK53mBt3TzH6Z52fV6fXPSZn5bZc97rNo76usnNEoQcTHaq"],
  "assetId": null,
  "recipient": "3N78bNBYhT6pt6nugc6ay1uW1nLEfnRWkJd",
  "feeAsset": null,
  "id": "8W9BkioPSWmPfDjcTFGaCy8vLEmcwkzJeSWno1s3Wra7",
  "timestamp": 1485529237072,
  "height": 56
}
```

The node itself stores transactions in a binary representation, not in the form of JSON, but at the time of an API request it encodes in JSON and sends it in that form. She also accepts it in the form of JSON. REST API nodes have the following useful endpoints:

`GET / transactions / info / {id}` - get information about one transaction

`GET / transactions / address / {address} / limit / {limit}` - get transactions at

`GET / blocks / at / {height}` - get a list of all transactions in the block

## Signature of transactions

All transactions have an important field - `senderPublicKey`, which defines on behalf of which account the action is performed. For a transaction ("action") to be considered valid, the signature of the transaction must match this public key (we are not considering cases with smart accounts now).

Cryptographic signature functions do not know anything about transactions, since they work with bytes. In the case of Waves, to sign transactions, it is necessary to arrange the bytes of the transaction in the correct order and transfer the signature functions along with the private key, as a result, we will receive the signature.

``` js
signature = sign (transactionBytes, privateKey)
```

The correct byte order for each transaction is described in the documentation. Cryptography is outside the scope of this book, but you can find examples of correct byte ordering for different types of transactions in the [Marshall JS Library on Github](https://github.com/wavesplatform/marshall).

Signing transactions is usually done on the side of the client application, but the node itself is also able to sign transactions sent via the API. You need to understand that a node will sign such a transaction with the private key that is specified in its configuration. **You cannot sign a transaction from an arbitrary sender using the REST API.** Many developers think that they need to get an API key in order to sign their transaction using a node, but this will only work if there is a private key from of the account that should perform the action.

## Transaction lifecycle

Let's take a look at all the stages of working with a transaction using the example of one action - sending a token from one user to another. We had a desire to send tokens from our account, from which we know the seed phrase (`A` in our example). We will send to an account with a public key `B`. First of all, we need to set the transaction parameters:

``` js
const params = {
  amount: 300000000,
  recipient: address ('B'),
  feeAssetId: null,
  assetId: null,
  attachment: 'TcgsE5ehTSPUftEquDt',
  fee: 100000,
}
```

We'll look at the transaction fields in the next part of this chapter. Now let's focus on the sequence of actions. To get the transaction along with the signature for our parameters, we use the `waves-transactions` library. To the `transfer` function, we pass the above parameters and the seed phrase. As a result, we get a JavaScript object that will contain all the fields we specified, as well as the signature in the `proofs` array, the transaction signing time (`timestamp`) and the sender's public key (account with the seed phrase `A`) in the `senderPublicKey`.

``` js
const signedTransferTx = transfer (params, 'A');
broadcast (signedTransferTx);
```

The library hides cryptography from us and the preparatory stage - the formation of the correct byte order for the signature. A signed transaction in the form of a JS object can be sent to any node that has an open API. The request is sent to `POST / transactions / broadcast` as JSON. The node will accept the transaction if there are no problems - the signature is valid, there are enough tokens on our account balance to complete the transaction, etc. A validated transaction will go to the UTX nodes where we sent the request, and it will then send information about this transaction to all the nodes with which it is connected.

### UTX

UTX is a list of transactions that are awaiting block hits. That is, someone sent them and the node accepted the transaction, but the transaction has not yet entered the block. Waves has certain quirks related to how these transactions are processed. How can a transaction end up in UTX? There are only 2 ways to do this:

- Someone will send a transaction to this node (using REST API or gRPC)
- The node will receive a binary protocol transaction from another node in the network

Ultimately, we can say that almost always transactions in the network come through the API, but it is not necessary that it was the API of this particular node.

A transaction that got into UTX has 2 options for further development of events:

- At some point in time, it will be added to the block by one of the miners
- The transaction will become invalid and will be removed from UTX (and can never get into the block). A transaction can become invalid for several reasons - the state of the blockchain has changed (another transaction entered the block and changed the sender's balance, the script on the account or asset now returns `false`, etc.), the transaction lifetime has expired (now in the Waves network the timestamp` of the transaction may differ by -2 or +1.5 hours from the current blockchain time).

The lifetime of a transaction can only expire because the network is 100% loaded. Nodes in Waves add transactions to the block one by one, starting with the most profitable for them (with the highest commission per byte). If at the time of sending our token transfer transaction, there were many transactions with a higher commission in UTX, then the miners will not add ours to the block, because the block has a size (1 MB) and number (6000 transactions) limit. Miners will produce blocks of maximum size with the most profitable transactions for them. If this continues for 90 minutes, then our transaction will become invalid. In fact, the sorting of a transaction in UTX by miners is done not only on the basis of the size of the commission, so we will consider the features of UTX operation in the future.

**For many newbies, it comes as a surprise that Waves blocks may contain transactions "from the past" and "from the future", which have `timestamp` 120 minutes less or 90 minutes longer than the present time.** In some cases, this must be taken into account when developing your applications.
