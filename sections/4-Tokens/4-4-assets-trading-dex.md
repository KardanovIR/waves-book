# Asset and DEX trading

After the possibility of creating your own tokens appeared, it was logical to make it possible to trade them (or, to be more precise, exchange) without the participation of intermediaries. To do this, a matcher was created in Waves (from the English "match" - match, match), which for a long time was part of the node, disabled by default (it was enough to enable the `waves.matcher.enabled` flag in the node configuration), which is now distributed as a node extension.

## How matcher works

The matcher accepts applications for exchanging tokens from users; in the Waves ecosystem, such applications are called Order. An example of such an "order" or "intention" of a user to make an exchange is presented below:

``` 'json
{
  "version": 3,
  "senderPublicKey": "FMc1iASTGwTC1tDwiKtrVHtdMkrVJ1S3rEBQifEdHnT2",
  "matcherPublicKey": "7kPFrHDiGw1rCm7LPszuECwWYL3dMf6iMifLRDJQZMzy",
  "assetPair": {
    "amountAsset": "BrjUWjndUanm5VsJkbUip8VRYy6LWJePtxya3FNv4TQa",
    "priceAsset": null
  },
  "orderType": "buy",
  "amount": 150000000,
  "timestamp": 1548660872383,
  "expiration": 1551252872383,
  "matcherFee": 300000,
  "proofs": [
    "YNPdPqEUGRW42bFyGqJ8VLHHBYnpukna3NSin26ERZargGEboAhjygenY67gKNgvP5nm5ZV8VGZW3bNtejSKGEa"
  ],
  "id": "Ho6Y16AKDrySs5VTa983kjg3yCx32iDzDHpDJ5iabXka",
  "sender": "3PEFvFmyyZC1n4sfNWq6iwAVhzUT87RTFcA",
  "price": 1799925005,
}
```

In addition to information about the sender, service fields and signature, each order contains information about which pair of tokens should be exchanged, the type of order (`buy` or` sell`), the expiration date of the order, the number of tokens to be exchanged and the price the user wants to exchange. Looking at the example above, you can understand that the user wants to exchange `Waves`, because` assetPair.priceAsset` is `null` and the order type is` buy`, for a token with assetId equal to `BrjUWjndUanm5VsJkbUip8VRYy6LWJePtxya3FNv4TQca` and [] with the name https://wavesexplorer.com/tx/BrjUWjndUanm5VsJkbUip8VRYy6LWJePtxya3FNv4TQa) which can be found in the explorer.

The number of tokens for exchange is indicated as 150,000,000 (we always remember that Waves has 8 decimal places, so he actually wants to exchange 1.5 Waves) for Zcash at a price of 17.99925005 per unit (Zcash also has 8 decimal places). In other words, if there is a person willing to sell 1 Zchah token in exchange for 17.99925005 Waves no later than the specified expiration date (1551252872383 or 02/27/2019 @ 7:34 am UTC), then the exchange will be made.

Let's imagine that another user sent a counter order for the same pair with the following parameters:

``` 'json
{
  "version": 3,
  "senderPublicKey": "FMc1iASTGwTC1tDwiKtrVHtdMkrVJ1S3rEBQifEdHnT2",
  "matcherPublicKey": "7kPFrHDiGw1rCm7LPszuECwWYL3dMf6iMifLRDJQZMzy",
  "assetPair": {
    "amountAsset": "BrjUWjndUanm5VsJkbUip8VRYy6LWJePtxya3FNv4TQa",
    "priceAsset": null
  },
  "orderType": "sell",
  "amount": 3000000000,
  "timestamp": 154866085334,
  "expiration": 1551252885334,
  "matcherFee": 300000,
  "proofs": [
    "YNPdPqEUGRW42bFyGqJ8VLHHBYnpukna3NSin26ERZargGEboAhjygenY67gKNgvP5nm5ZV8VGZW3bNtejSKGEa"
  ],
  "id": "Ho6Y16EFvFmyyZC1n4sfNWq6iwAVhzUT87RTFcAabXka",
  "sender": "3PAKDrySs5VTa983kjg3yCx32iDzDHpDJ5i",
  "price": 1799925005,
}
```

The sender of this order wants to make a reverse exchange operation (`Zcash` ->` Waves`) at the same price, but wants to exchange 30 Zcash.

Both orders are sent to one matcher with the public key `7kPFrHDiGw1rCm7LPszuECwWYL3dMf6iMifLRDJQZMzy`, which, after seeing the match of parameters (pair, price) and the validity of the signature and expiration date, will form an exchange transaction -` Exchange`. At the same time, the first order will be fully executed (all 1.5 Waves will be exchanged for Zcash), and the second will only partially wait for a suitable order for the exchange. An approximate scheme of work is shown in the figure:

![How matching works](../../assets/4-4-1-how-dex-works.png "How matching workks")

We will look at an example of an `Exchange` transaction in the next chapter, which is devoted to transactions, let's now talk about the features of the matcher.

## Matcher functions

Matcher is the heart of Waves-based decentralized exchanges (DEXs), the most popular of which is waves.exchange. Let's take a look at how the matcher works and the whole decentralized exchange procedure.

The matcher accepts orders for the purchase or sale of tokens from everyone who wants them, stores them in the orderbook and, when a match is found, forms an exchange transaction and sends it to the blockchain (sends it to the node, which already adds to the block, directly exchanging tokens on user balances) ...

Let's describe the entire path for exchanging tokens:

1. The user creates an order for the exchange, indicating a pair of tokens, the type of order (what he wants to exchange for), the exchange price, the number of tokens to exchange, validity period, the size of the commission for the matcher and to which matcher he wants to send his order.
2. The user signs the order and sends it to the matcher via API.
3. The matcher checks the validity of the order signature, the correctness of the specified dates, the commission specified by the user and the presence of tokens for exchange and commission on the user's balance (for this, he makes a request to the blockchain node).
4. If the exchange is made in a pair, where one or both tokens are smart assets, the matcher executes the asset script and considers the order as valid only upon receiving `true`. In case of receiving `false` or an exception, the matcher considers the order invalid and it is rejected.
5. If there is a counter-order in the orderbook, with which an exchange operation can be performed, the matcher generates an `Exchange` transaction, signs it and sends it to the blockchain node. If there was no suitable order in the order book, then the newly created order is added to the order book, where it will remain until a correct counter order is found or the order expires. It is worth noting that the exchange transaction is done on behalf of the match and with the signature of the match, and not on behalf of the users; accordingly, the commission for getting into the blockchain is paid by the matcher.
6. When the blockchain node receives an `Exchange` transaction, it validates it and the orders included in it (the exchange transaction includes the orders themselves too) and adds it to the block.
7. The state of account balances in the blockchain changes in accordance with the parameters of the `Exchange` transaction.

## Features of the exchange in the Waves blockchain

Decentralized exchange in Waves can be carried out without a matcher: two users can combine their orders in an `Exchange` transaction and send them to the network on behalf of a third account (or one of them), but in view of the inconvenience of this method, most exchange transactions are made using a matcher ...

Each individual matcher is a centralized entity and is controlled by one person or team, but why then do we call the exchange decentralized, and exchanges using the matcher - DEX? It is necessary to understand the main difference between ordinary centralized exchanges and DEX - control over user funds. Centralized exchanges have direct access to users' funds and their keys, so they can do whatever they want with them, while the matcher in Waves has access only to user intentions (orders) and cannot do anything with your tokens directly. The worst thing that a matcher can do is exchange at a not the best price available on the market or not complete an exchange transaction, although the counter order was in the order book.

Are there more decentralized solutions? Of course there are, there are completely decentralized exchanges, but with full decentralization, it is impossible to solve the problem of [front-running](https://www.investopedia.com/terms/f/frontrunning.asp) blockchain nodes (in the Waves matcher scheme, nodes cannot implement such an attack, however, the matcher itself, which you trust).

Another feature of the exchange is that there are many matchers in the Waves ecosystem, but they do not exchange orders with each other. In fact, you trust one matchmaker when you send him your order. You trust him that he will perform the operation and he will do it honestly (for example, he will not let your order forward, which came later). It is this trust that prevents the exchange of orders between matchers: most likely, you are ready to trust one match, but you are not ready to trust all of them, because any of the many may turn out to be a "pest".

The presence of centralized matching allows to achieve excellent throughput of thousands of generated `Exchange` transactions per second. The maximum possible speed of the matcher is now much higher than the throughput of the blockchain. Of course, trading in high-frequency trading (HFT) will not work, but there are a large number of bots that make hundreds of transactions per second. You can find examples of bots in Github, the most popular of which are [Scalping Bot](https://docs.wavesplatform.com/en/building-apps/waves-api-and-sdk/examples/trading-bot) and [Grid Trading Bot](https://github.com/PyWaves/BlackBot).