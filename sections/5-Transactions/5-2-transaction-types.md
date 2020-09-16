# Transaction types

To use the Waves blockchain as efficiently as possible and understand all of its capabilities, you need to understand the types of transactions and their features. In this section, we'll break down all types and discuss potential pitfalls.

There are two types of transactions in Waves that are currently not used and that you will definitely not use when working on the main network - `Genesis` and` Payment` transactions.

## Genesis transaction (type = 1) [deprecated]

`Genesis` transactions were only in the [very first block of the blockchain] (http://nodes.wavesplatform.com/blocks/at/1) and were responsible for the distribution of pre-issued tokens (there were 100 million of them). Let's see what the genesis block looked like.

** Note: ** Many people confuse the `genesis` block and the` genesis` transaction. The `Genesis` block is the very first block in the blockchain network (in all blockchains it is customary to call it that), which differs from the rest of the blocks only by the absence of a reference to the previous block, since the previous block simply did not exist. The `Genesis` block contains the` genesis` transactions that are responsible for the initial distribution of issued Waves tokens. The very first block on the Waves network is shown below:

`` 'json
{
  "reference": "67rpwLCuS5DGA8KGZXKsVQ7dnPb9goRLoKfgGbLfQg9WoLUgNY77E2jT11fem3coV9nAkguBACzrU1iyZM4B8roQ",
  "blocksize": 500,
  "signature": "FSH8eAAzZNqnG8xgTZtz5xuLqXySsXgAjmFEC25hXMbEufiGjqWPnGCZFt6gLiVLJny16ipxRNAkkzjjhqTjBE2",
  "totalFee": 0,
  "nxt-consensus": {
    "base-target": 153722867,
    "generation-signature": "11111111111111111111111111111111"
  },
  "fee": 0,
  "generator": "3P274YB5qseSE9DTTL3bpSjosZrYBPDpJ8k",
  "transactionCount": 6,
  "transactions": [
    {
      "type": 1,
      "id": "2DVtfgXjpMeFf2PQCqvwxAiaGbiDsxDjSdNQkc5JQ74eWxjWFYgwvqzC4dn7iB1AhuM32WxEiVi1SGijsBtYQwn8",
      "fee": 0,
      "timestamp": 1465742577614,
      "signature": "2DVtfgXjpMeFf2PQCqvwxAiaGbiDsxDjSdNQkc5JQ74eWxjWFYgwvqzC4dn7iB1AhuM32WxEiVi1SGijsBtYQwn8",
      "recipient": "3PAWwWa6GbwcJaFzwqXQN5KQm7H96Y7SHTQ",
      "amount": 9999999500000000
    },
    {
      "type": 1,
      "id": "2TsxPS216SsZJAiep7HrjZ3stHERVkeZWjMPFcvMotrdGpFa6UCCmoFiBGNizx83Ks8DnP3qdwtJ8WFcN9J4exa3",
      "fee": 0,
      "timestamp": 1465742577614,
      "signature": "2TsxPS216SsZJAiep7HrjZ3stHERVkeZWjMPFcvMotrdGpFa6UCCmoFiBGNizx83Ks8DnP3qdwtJ8WFcN9J4exa3",
      "recipient": "3P8JdJGYc7vaLu4UXUZc1iRLdzrkGtdCyJM",
      "amount": 100000000
    },
    {
      "type": 1,
      "id": "3gF8LFjhnZdgEVjP7P6o1rvwapqdgxn7GCykCo8boEQRwxCufhrgqXwdYKEg29jyPWthLF5cFyYcKbAeFvhtRNTc",
      "fee": 0,
      "timestamp": 1465742577614,
      "signature": "3gF8LFjhnZdgEVjP7P6o1rvwapqdgxn7GCykCo8boEQRwxCufhrgqXwdYKEg29jyPWthLF5cFyYcKbAeFvhtRNTc",
      "recipient": "3PAGPDPqnGkyhcihyjMHe9v36Y4hkAh9yDy",
      "amount": 100000000
    },
    {
      "type": 1,
      "id": "5hjSPLDyqic7otvtTJgVv73H3o6GxgTBqFMTY2PqAFzw2GHAnoQddC4EgWWFrAiYrtPadMBUkoepnwFHV1yR6u6g",
      "fee": 0,
      "timestamp": 1465742577614,
      "signature": "5hjSPLDyqic7otvtTJgVv73H3o6GxgTBqFMTY2PqAFzw2GHAnoQddC4EgWWFrAiYrtPadMBUkoepnwFHV1yR6u6g",
      "recipient": "3P9o3ZYwtHkaU1KxsKkFjJqJKS3dLHLC9oF",
      "amount": 100000000
    },
    {
      "type": 1,
      "id": "ivP1MzTd28yuhJPkJsiurn2rH2hovXqxr7ybHZWoRGUYKazkfaL9MYoTUym4sFgwW7WB5V252QfeFTsM6Uiz3DM",
      "fee": 0,
      "timestamp": 1465742577614,
      "signature": "ivP1MzTd28yuhJPkJsiurn2rH2hovXqxr7ybHZWoRGUYKazkfaL9MYoTUym4sFgwW7WB5V252QfeFTsM6Uiz3DM",
      "recipient": "3PJaDyprvekvPXPuAtxrapacuDJopgJRaU3",
      "amount": 100000000
    },
    {
      "type": 1,
      "id": "29gnRjk8urzqc9kvqaxAfr6niQTuTZnq7LXDAbd77nydHkvrTA4oepoMLsiPkJ8wj2SeFB5KXASSPmbScvBbfLiV",
      "fee": 0,
      "timestamp": 1465742577614,
      "signature": "29gnRjk8urzqc9kvqaxAfr6niQTuTZnq7LXDAbd77nydHkvrTA4oepoMLsiPkJ8wj2SeFB5KXASSPmbScvBbfLiV",
      "recipient": "3PBWXDFUc86N2EQxKJmW8eFco65xTyMZx6J",
      "amount": 100000000
    }
  ],
  "version": 1,
  "timestamp": 1460678400000,
  "height": 1
}
``

You can see that there were 6 public keys-recipients of the newly issued Waves tokens. All transactions have the same `timestamp` and they were all free (the` fee` is zero) because there was nothing else to pay the `fee` at the time these transactions were created.

These transactions are not created manually, they are generated automatically by the special utility `genesis-generator`, which is in the node repository. You may need to do this if you want to launch your private blockchain. How to do this (and why) we will look at in one of the next chapters.

> Attentive readers may ask why `9999999500000000` tokens are sent in the very first transaction if only 100 million were issued? In Waves, all transactions are invoiced in minimal indivisible token units (fraction). The Waves token has 8 decimals, so the minimum unit is one hundred millionth. If the `amount` field of any transaction contains the value` 100000000` (10 ^ 8), this actually means one whole Waves token. In the case of a `genesis` transaction,` 9999999500000000` means 99,999,995 tokens or 9999999500000000 minimum units. Waves' smallest units are often referred to as WAVELET.

## Payment transaction (type = 2) [deprecated]

At the time of the launch of the Waves blockchain, only 2 types of transactions were implemented - the already considered type `genesis` and` payment`, which allowed transferring Waves tokens from one account to another. Examples of the `payment` transaction in JSON representation can be found in [block 2000] (http://nodes.wavesplatform.com/blocks/at/2000).

`` 'json
    {
      "senderPublicKey": "6q5VhGeTanU5T8vWx6Jka3wsptPKSSHA9uXHwdvBMTMC",
      "amount": 10000000000,
      "sender": "3PGj6P4Mfzgo24i8cG3nhLU6uktF6s5LVCT",
      "feeAssetId": null,
      "signature": "3gzk9QyfqQGvsU8A4zMMorpKTcFpdG7UtC4c5E7ds9MGMCMSyp6JZymQJoCjUSJQ8AaSWQDQwNmQ5F46ud4ofA5o",
      "proofs": [
        "3gzk9QyfqQGvsU8A4zMMorpKTcFpdG7UtC4c5E7ds9MGMCMSyp6JZymQJoCjUSJQ8AaSWQDQwNmQ5F46ud4ofA5o"
      ],
      "fee": 1,
      "recipient": "3P59ixWkqiEnL7RJoXtZewgbatKBZo8bG15",
      "id": "3gzk9QyfqQGvsU8A4zMMorpKTcFpdG7UtC4c5E7ds9MGMCMSyp6JZymQJoCjUSJQ8AaSWQDQwNmQ5F46ud4ofA5o",
      "type": 2,
      "timestamp": 1465865163143
    }
``

A `Payment` transaction can only send Waves tokens (not other ones issued on the platform) from one address to another. It became obsolete with the advent of `Transfer` transactions that can send both Waves tokens and custom tokens, so now` Payment` is no longer used anywhere.

## Issue transaction (type = 3)

In the section about tokens, we have already discussed in detail how to release your asset using the `Issue` transaction, so now I will not dwell on how to use it. One has only to say that the distinctive feature of the `Issue` transaction is that there are 2 fundamentally different options for issuing a token:

- issuance of a unique token (aka non-fungible token, NFT)
- issue of a regular token

Issuing a unique token differs in that the parameters `amount`,` reissuable`, `decimals` must have predefined values ​​-` 1`, `false` and` 0`, respectively. If this condition is met, the minimum commission will be 0.001 Waves. If these parameters differ (at least one of the parameters), then the token is considered normal and the minimum issue commission will be 1 Waves.

An example JSON representation of the `Issue` transaction is shown below:

`` 'json
{
    senderPublicKey: "7nSKRN4XZiD3TGYsMRQGQejzP7x8EgiKoG2HcY7oYv6r",
    quantity: 210000000,
    signature: "3Vj8M9tkVZmnjdYAKKN3GzAtV9uQDX5hhgUfXQDdvZsk2AmvqQum3oGBJqdjALVHXX2ibLAZHeruwjNXR46WgBnm",
    fee: 100000000,
    description: "",
    type: 3,
    version: 1,
    reissuable: true,
    sender: "3PAJ6bw7kvSPf6Q9kAgfSLzmpFspZmsi1ki",
    feeAssetId: null,
    proofs: [
"3Vj8M9tkVZmnjdYAKKN3GzAtV9uQDX5hhgUfXQDdvZsk2AmvqQum3oGBJqdjALVHXX2ibLAZHeruwjNXR46WgBnm"
],
    script: null,
    assetId: "oWgJN6YGZFtZrV8BWQ1PGktZikgg7jzGmtm16Ktyvjd",
    decimals: 1,
    name: "ihodl",
    id: "oWgJN6YGZFtZrV8BWQ1PGktZikgg7jzGmtm16Ktyvjd",
    timestamp: 1528867061493,
    height: 1039500
}
``

> Important: if a token is issued without a script, then it cannot be added to it later, so if you want to add a script in the future, but do not have this script yet, then specify `AwZd0cYf` as a script (` true` in the compiled base64 version)

## Tranfer transaction (type = 4)

The `Tranfser` transaction came to replace the` Payment` transaction, because `Payment` did not allow sending tokens created with the` Issue` transaction. Currently, the `Transfer` transaction is the most frequent one according to [dev.pywaves.org] (http://dev.pywaves.org/txs/) and accounts for about 70% of transactions on the network. Sending a `Transfer` transaction is similar to sending most token-related transactions:

`` `js

const {transfer} = require ('@ waves / waves-transactions');

const seed = 'example seed phrase';

// Transfering 3 WAVES
const params = {
  amount: 300000000,
  recipient: '3P23fi1qfVw6RVDn4CH2a5nNouEtWNQ4THs',
  feeAssetId: null,
  assetId: null,
  attachment: 'TcgsE5ehTSPUftEquDt',
  fee: 100000,
}

const signedTransferTx = transfer (params, seed);
broadcast (signedTransferTx);

``

The example above will generate a transaction from the account with the seed phrase `example seed phrase`, automatically substitute additional fields (` timestamp`, `senderPublicKey`,` proofs`) into the created transaction, sign it with a private key from the specified seed phrase and add the transaction signature to the array `proofs`.

The recipient of the transaction is the address `3P23fi1qfVw6RVDn4CH2a5nNouEtWNQ4THs`, and we send Waves tokens. To calculate how many tokens are sent, we need to remember that the transaction indicates the value of `amount` in the minimum fractions of this token. To get it in whole units, you need to divide `300000000` by` 10 ^ decimals`. `300000000 / (10 ^ 8) = 3`.

The `Transfer` transaction has several interesting features:

- It supports sponsoring transactions, so in the field `feeAssetId` you can specify the` assetId` of some token that you have and is sponsored by the creator, then you will pay a fee in this token. In our case, `null` is specified, so the commission will be paid in Waves tokens.
- The transaction has an `attachment` field that can contain up to 140 bytes of information. In the `waves-transactions` library, the` attachment` value must be passed in the `base58` format, so you see` TcgsE5ehTSPUftEquDt`, although in the "human" representation it can be read as `HelloWavesBook`.

The `Transfer` transaction allows you to specify 0 in the` amount` field, that is, to send 0 tokens to the recipient. Some users use this feature to send `Transfer` transactions as" messages "or events that may trigger other actions outside the blockchain.

An example of a `Transfer` transaction is shown below:

`` 'json
{
    senderPublicKey: "CXpZvRkJqBfnAw3wgaRbeNjtLJcithoyQQQSzGQZRF3x",
    amount: 32800000000,
    signature: "4cR2NAor9WjeTbysg2QMerkgymc5RLrX8PPjdXkUkWEc7BFBKMCCj8RKF7X1UchbvtEGoqGyQh62MDq5KoXsnCzg",
    fee: 100000,
    type: 4,
    version: 1,
    attachment: "",
    sender: "3P4FoAakEyk78TxUBcXH4uZXLaSE5BiDgjz",
    feeAssetId: null,
    proofs: [
"4cR2NAor9WjeTbysg2QMerkgymc5RLrX8PPjdXkUkWEc7BFBKMCCj8RKF7X1UchbvtEGoqGyQh62MDq5KoXsnCzg"
],
    assetId: null,
    recipient: "3PNX6XwMeEXaaP1rf5MCk8weYeF7z2vJZBg",
    feeAsset: null,
    id: "JAutkv1Nk4xVrkb4fkacS4451VvyHC3iJtEDfBRD7rwr",
    timestamp: 1528867058828,
    height: 1039500
}
``

## Reissue transaction (type = 5)

If, when issuing a token using the `Issue` transaction, set the` reissuable` flag to `true`, then the creator of the token gets the opportunity to reissue the token. The history of `reissuable` transactions in Waves is a bit strange, as you can find tokens on the blockchain that had the` reissuable` flag equal to `false` at the time of creation, but were reissued. These tokens was only 4, so their assetId: `6SGeUizNdhLx8jEVcAtEsE7MGPHGYyvL2chdmPxDh51K`,` UUwsxTvvG7LiN7yaAKvNU48JHcSwQ3q1HvsXyAgc9fL`, `3DhpxLxUrotfXHcWKr4ivvLNVQUueJTSJL5AG4qB2E7U`,` CH1LNr9ASLVqSHDb482ZzSA5rBVLDtF5QbfECGgwE8bh`. This became possible thanks to a bug in the node's code, which allowed re-issuing non-reissued tokens. Don't be surprised if you find reissued `non-reissuable` tokens in the history of mainnet Waves.

An example of a `Reissue` transaction is shown below:

`` 'json
{
    senderPublicKey: "4X2Fv5XaDwBj2hjRghfqmsQDvBHqSa2zBUgZPDgySSJG",
    quantity: 10000000000000000,
    signature: "5nNrLV46rVzQzeScz3RmZF4rzaV2XaSjT9kjtHoyrBzAj3iVZM9Gy6t5Paho7xRx9dyqzj1AKyWYQsgL2nFa7jYU",
    fee: 1000000,
    type: 5,
    version: 1,
    reissuable: true,
    sender: "3P6ms9EotRX8JwSrebeTXYVnzpsGCrKWLv4",
    feeAssetId: null,
    chainId: null,
    proofs: [
"5nNrLV46rVzQzeScz3RmZF4rzaV2XaSjT9kjtHoyrBzAj3iVZM9Gy6t5Paho7xRx9dyqzj1AKyWYQsgL2nFa7jYU"
],
    assetId: "AC3KZWmywTEYrcQwpjg4sQiWxkZ2TZmv81JAvDmsoQvy",
    id: "6qd8QbnFrKEibTr26JyNh1hc4KaafGQYStyShtXdNk3v",
    timestamp: 1528733511933,
    height: 1037381
}
``

## Burn transaction (type = 6)

A token burn transaction allows you to burn any number of tokens of the same type. The only condition is that these tokens must be on your account and the token script must not prohibit burning.

An example of a `Burn` transaction is shown below:

`` 'json
{
    senderPublicKey: "EhuzuzEWHhZGo1th6YGy34AecoRP4sVi863xXCQUmgUT",
    amount: 10000000000,
    signature: "5HdfqY47Pm4G6h67K9ZpN7jQ4NKr9hsNsmTAtyFD5FhBPr3J9kNxodhYn6hMSieKE7UmYZvSohv7XJpyjKvGCfTC",
    fee: 100000,
    type: 6,
    version: 1,
    sender: "3PAjApsrjJWGmRDbGo65gGgrN2hFJroAZDC",
    feeAssetId: null,
    proofs: [
"5HdfqY47Pm4G6h67K9ZpN7jQ4NKr9hsNsmTAtyFD5FhBPr3J9kNxodhYn6hMSieKE7UmYZvSohv7XJpyjKvGCfTC"
],
    assetId: "56w2Jbj8MGKwSWyTXvCzkqKKHiyX7C2zrgCQb2CEwM52",
    id: "EzeiYzYPwyJNEgofQrE23rpqaYERjUSnCaXZ84vUDoec",
    timestamp: 1528814759445,
    height: 1038647
}
``

## Exchange transaction (type = 7)

In the previous chapter, we talked a lot about the token exchange procedure, the work of the order matcher and the `Exchange` transactions. Among other things, they touched upon the topic that a transaction contains orders, and that is why this transaction is the most complex in JSON representation:

`` 'json
{
    senderPublicKey: "7kPFrHDiGw1rCm7LPszuECwWYL3dMf6iMifLRDJQZMzy",
    amount: 74,
    signature: "2p1BS5BPkMW4C3C6vL8MsrQ8CBQRQqDoYieaZcxeMAq5zvAsm6T4N5DDN6MfPx8emVmbHfibZRsok2v2Ss45e1mj",
    fee: 300000,
    type: 7,
    version: 1,
    sellMatcherFee: 63610,
    sender: "3PJaDyprvekvPXPuAtxrapacuDJopgJRaU3",
    feeAssetId: null,
    proofs: [
"2p1BS5BPkMW4C3C6vL8MsrQ8CBQRQqDoYieaZcxeMAq5zvAsm6T4N5DDN6MfPx8emVmbHfibZRsok2v2Ss45e1mj"
],
    price: 103526336,
    id: "GHKhG3CWNfXAPWprk9bHSE4rxN6QfNDe3d3rZGaDLWhm",
    order2: {
    version: 1,
    id: "5C8qLi2eK92CJtBqXbL9pMuQ2R9VpRMaJ6NGACfxMBCn",
    sender: "3P7DsCo8TN5t1PNz45exhLe6vKFkTQJYrNb",
    senderPublicKey: "6mYVd69bZsLYW9gpxu3Vjneaf4xpZPnKYiLFuGXJQKQw",
    matcherPublicKey: "7kPFrHDiGw1rCm7LPszuECwWYL3dMf6iMifLRDJQZMzy",
    assetPair: {
    amountAsset: "725Yv9oceWsB4GsYwyy4A52kEwyVrL5avubkeChSnL46",
    priceAsset: null
},
    orderType: "sell",
    amount: 349,
    price: 103526336,
    timestamp: 1528814695617,
    expiration: 1528814995617,
    matcherFee: 300000,
    signature: "4DSQvXBLA4U4mtTRzjz62Ci757TZsys8phWbfnCmwvrKDhYFfB8kEknJ9fknAfWkJua7wN4EPbdrSLPgRShaxTsj",
    proofs: [
"4DSQvXBLA4U4mtTRzjz62Ci757TZsys8phWbfnCmwvrKDhYFfB8kEknJ9fknAfWkJua7wN4EPbdrSLPgRShaxTsj"
]
},
    order1: {
    version: 1,
    id: "Eiy6wSzu3aZu3V5Mi7VN54Vmu5KQE18nEQ3j5bJU2WYK",
    sender: "3PMFLMN9GG1coCXRn26vUmF2vtCCd4RDWRR",
    senderPublicKey: "Dk3r1HwVK1Ktp3MJCoAspNyyRpLFcs2h5SKsoV5F3Rvd",
    matcherPublicKey: "7kPFrHDiGw1rCm7LPszuECwWYL3dMf6iMifLRDJQZMzy",
    assetPair: {
    amountAsset: "725Yv9oceWsB4GsYwyy4A52kEwyVrL5avubkeChSnL46",
    priceAsset: null
},
    orderType: "buy",
    amount: 74,
    price: 103526336,
    timestamp: 1528814695596,
    expiration: 1528814995596,
    matcherFee: 300000,
    signature: "5kM8NRVxu4xtDUwz7GCVqyHbeszjXheJn1f7Q5Kpa4zdkeXe8k1kNENAU1YVNXyxNjMHCwtY9mwUkBpZWPo2CHWf",
    proofs: [
"5kM8NRVxu4xtDUwz7GCVqyHbeszjXheJn1f7Q5Kpa4zdkeXe8k1kNENAU1YVNXyxNjMHCwtY9mwUkBpZWPo2CHWf"
]
},
    buyMatcherFee: 300000,
    timestamp: 1528814695635,
    height: 1038644
}
``

As you can see, the transaction contains the fields `order1` (an order of the` buy` type) and `order2` (an order of the` sell` type). There is also a signature in the `proofs` array, which is the signature of the matcher (not the order senders!), The size of the commission for the match (` sellMatcherFee`), the commission for the node that will mine the block (the `fee` field for the entire transaction and not for the order ).

The values ​​of the `matcherPublicKey` fields in the orders must match the` senderPublicKey` field for the `Exchange` transaction, which ensures that no other matcher can perform an exchange operation using these orders.

Forming an `Exchange` transaction in most cases is not necessary for users and developers, therefore it is not supported in many libraries for different programming languages. Another thing is orders, the formation of which is necessary for bots and many user interfaces. Formation of an order using `waves-tranasctions` does not fundamentally differ from the formation of a transaction:

`` `js
const {order} = require ('@ waves / waves-transactions')

const seed = 'b716885e9ba64442b4f1263c8e2d8671e98b800c60ec4dc2a27c83e5f9002b18'

const params = {
  amount: 100000000, // 1 waves
  price: 10, // for 0.00000010 BTC
  priceAsset: '8LQW8f7P5d5PZM7GtZEBgaqRPGSzS3DfPuiXrURJ4AJS',
  matcherPublicKey: '7kPFrHDiGw1rCm7LPszuECwWYL3dMf6iMifLRDJQZMzy',
  orderType: 'buy'
}


const signedOrder = order (params, seed)
``

Please note that unlike the examples with transactions, the example does not use the `broadcast` function to send the transaction, because the` broadcast` sends the transaction to the node, and we need to send the order to the matcher. You can find information about the matcher API in the [waves.exchange documentation] (https://docs.waves.exchange/en/waves-matcher/matcher-api), since Waves.exchange works on the basis of the matcher.

## Lease and Lease Cancel transactions (types 8 and 9)

At the very beginning of this book, we touched on the topic of leasing a little, which allows you to lease your tokens to other nodes "for rent" to generate blocks. To do this, you need to send a transaction of type `Lease`.

`` `js
const {lease} = require ('@ waves / waves-transactions')

const seed = 'example seed phrase'

const params = {
  amount: 100,
  recipient: '3P23fi1qfVw6RVDn4CH2a5nNouEtWNQ4THs',
  fee: 100000
}

const signedLeaseTx = lease (params, seed)
broadcast (signedLeaseTx);
``

As you can see, the transaction is extremely simple, we indicate the recipient in the `recipient` field in the form of an address or an alias (we'll talk about them below) and the amount we want to lease. It should be borne in mind that these tokens will participate in mining only 1000 blocks after they are leased.

The sender of the lease can cancel the lease at any time, again gaining access to them for trading, transfers or mining at their address. To do this, you need to send the transaction `LeaseCancel`:

`` `js
const {cancelLease} = require ('@ waves / waves-transactions')

const seed = 'example seed phrase'

const params = {
  leaseId: '2fYhSNrXpyKgbtHzh5tnpvnQYuL7JpBFMBthPSGFrqqg',
  senderPublicKey: '3SU7zKraQF8tQAF8Ho75MSVCBfirgaQviFXnseEw4PYg', // optional, by default derived from seed
  timestamp: Date.now (), // optional
  fee: 100000, // minimal value
  chainId: 'W' // optional
}

const signedCancelLeaseTx = cancelLease (params, seed)
broadcast (signedCancelLeaseTx);
``

A lease cancellation transaction requires the transfer of the `ID` of the lease transaction. You can only cancel the entire lease transaction. For example, if you lease 1000 Waves to any node of one transaction, you will not be able to withdraw part of this amount - it can only be canceled in its entirety.

Please also note that this transaction specifies the `chainId`, while in the lease sending transaction, this is not required. Try to guess why.

> The answer is simple: in the lease sending transaction there is a `recipient` field, where the address is indicated (which already contains the chainId in itself), and in the lease cancellation transaction there is no` recipient` field, therefore, to make it impossible to send the same transactions in different networks, you have to specify the network byte. But if you use the `waves-transactions` library, then it will substitute the network byte for the Mainnet itself to simplify development and make your code cleaner and easier.

Another difference between canceling a lease and sending it on lease is that the cancellation takes effect as soon as it enters the blockchain, without waiting for 1000 blocks.

## Alias ​​transaction (type = 10)

Waves has a unique feature that many other blockchains do not have - the presence of aliases. It is sometimes extremely inconvenient to use addresses for transactions, they are long and impossible to remember, so each account can create aliases for itself. Alias ​​can be simple and easy to remember. In any transaction on the Waves network, in the `recipient` field, you can specify not only the address, but also the alias.

Ethereum has a slightly similar concept of ENS, which is built on the principles of DNS, with different levels (namespace) and management through smart contracts. In Waves, aliases are part of the protocol and all are in the global namespace, without division into domains and subdomains. One account can create an unlimited number of aliases by sending a special type of transaction:

`` `js
const {alias} = require ('@ waves / waves-transactions')

const seed = 'example seed phrase'

const params = {
  alias: 'new_alias',
  chainId: 'W'
}

const signedAliasTx = alias (params, seed)
broadcast (signedAliasTx)
``

Alias ​​can consist of:

- letters of the Latin alphabet in lower case
- numbers
- points
- underscores
- hyphen characters
- signs `@`

The alias must be between 4 and 30 characters long. The problem with aliases on the Waves network is that they are all in the global space and cannot be repeated, so there are accounts with more than 2,000 aliases - a kind of cybersquatting in the blockchain.

## Mass transfer transaction (type = 11)

At the dawn of its history, Waves was known as a blockchain with very easy token issuance, and the community's natural desire was to simplify the next step of many token issuance campaigns - distributing tokens to recipients. To meet this demand, a transaction was created that allows you to send tokens from one address to many. There are only 2 restrictions - there can be no more than 100 recipients, and only 1 type of token is sent to all of them (you cannot make a `MassTransfer` and send the` A` token to the first half of the addresses, and `B` to the second).

`` `js
const {massTransfer} = require ('@ waves / waves-transactions')

const seed = 'example seed phrase'

const params = {
  transfers: [
    {
      amount: 100,
      recipient: '3P23fi1qfVw6RVDn4CH2a5nNouEtWNQ4THs',
    },
    {
      amount: 200,
      recipient: '3PPnqZznWJbPG2Z1Y35w8tZzskiq5AMfUXr',
    },
  ],
  // senderPublicKey: 'by default derived from seed',
  // timestamp: Date.now (),
  // fee: 100000 + transfers.length * 50000,
}

const signedMassTransferTx = massTransfer (params, seed);
broadcast (signedMassTransferTx);
``

In addition to the convenience of working with such a transaction, compared to sending 100 transactions of the `Transfer` type, such a transaction is also cheaper. If the minimum commission for `Transfer` is 0.001 Waves (100000 Wavelet), then the minimum commission for` MassTransfer` is calculated by the formula:

`100000 + transfers.length * 50000`

That is, sending 100 `Transfer` transactions will cost us 0.1 Waves, while sending one` MassTransfer` with 100 recipients in just 0.051 Waves is almost 2 times cheaper.

## Data transaction (type = 12)

Особенность Waves, которая делает его крайне удобным блокчейном для работы с данными, является наличие `Data` транзакций, которые появились в апреле 2018 года и позволили записывать данные в блокчейн в очень удобном формате.

С введением `Data` транзакций, у каждого аккаунта появилось key-value хранилище, в которое можно записывать данные четырех типов: строки, числа, булевые значения и массивы байт.

Хранилище аккаунта не имеет ограничения по общему размеру данных, которое можно туда записывать, но есть ограничения на:

- размер одной транзакции записи данных в хранилище не более 140 килобайт. Комиссия за транзакцию зависит от размера транзакции и считается по формуле `100000 + bytes.length * 100000`.
- размер данных на один ключ не более 32 килобайт
- размер ключа не более 100 символов. Ключами в хранилище могут быть только строки в формате UTF-8.

Давайте посмотрим как записать данные с помощью JavaScript библиотеки:

`` `js
const { data } = require('@waves/waves-transactions')

const seed = 'example seed phrase'

const params = {
  data: [
    { key: 'integerVal', value: 1 },
    { key: 'booleanVal', value: true },
    { key: 'stringVal', value: 'hello' },
    { key: 'binaryVal', value: [1, 2, 3, 4] },
  ],
  // senderPublicKey: 'by default derived from seed',
  //timestamp: Date.now(),
  //fee: 100000 + bytes.length * 100000
}

const signedDataTx = data(params, seed);
broadcast(signedDataTx);

``

Надо понимать, что состояние хранилища со всеми ключами и значениями может прочитать любой пользователь, более того, значение по любому ключу доступно всем смарт-контрактам в сети, будь то децентрализованное приложение, смарт ассет или смарт аккаунт.

Данные по ключу могут перезаписываться неограниченное количество раз, если обратное не указано в контракте аккаунта. В дальнейшем мы рассмотрим, как реализовать на аккаунте read-only пары, которые могут быть записаны только один раз и не могут быть изменены или удалены.

Многие пользователи ожидают, что у ассетов тоже есть свои key-value хранилища, однако это не так. Только аккаунт имеет такое хранилище, поэтому если вам необходимо записывать данные для использования ассетом - записывайте в аккаунт, который выпустил токен. Вы можете так же записывать в любой другой аккаунт, ведь можно читать любые ключи любых аккаунтов в коде вашего смарт-ассета.

Другой частый вопрос - "Можно ли удалить из хранилища ключ?". До недавнего времени такое было невозможно, но с релизом языка программирования Ride версии 4 это становится возможным. Чтобы сейчас не смешивать и Ride, и транзакции, давайте отложим рассмотрение кода Ride до следующей главы. Лучше сейчас поговорим по получение данных из хранилища аккаунта. Это можно сделать с помощью REST запроса к API ноды:

1. Эндпоинт `/addresses/data/{address}?matches={regexp}` позволяет получить все данные из хранилища, при необходимости фильтруя ключи по регулярному выражению, передаваемому как параметр `matches`. Фильтрация по значениям пока не поддерживается в ноде.
2. Эндпоинт `/addresses/data/{address}/{key}` позволяет получить значение одного ключа в хранилище одного аккаунта.

В библиотеке `waves-transactions` есть дополнительные методы, которые позволяют делать это без необходимости писать самим логику отправки запроса к API. Ниже пример получения всего состояния хранилища и значения по одному ключу:

`` `js
const { nodeInteractions } = require('@waves/waves-transactions')

const address = '3P23fi1qfVw6RVDn4CH2a5nNouEtWNQ4THs'

const wholeStorage = await accountData(address);
const oneKeyValue = await accountDataByKey(address, "demoKey");

console.log(wholeStorage, oneKeyValue);

``

Как видите, все достаточно несложно. У API ноды Waves есть несколько особенностей, некоторые из которых хорошо бы знать до начала работы, чтобы в самый неподходящий момент не получить ошибку в момент исполнения вашего кода. К таким особенностям работы я бы отнес следующее:

1. Нода предназначена в первую очередь для поддержания работы блокчейна, а не оптимальной работы с API, поэтому запросы всего хранилища для аккаунтов с большим количеством данных могут приводить к проблемам. Я бы никогда и никому не рекомендовал запрашивать весь стейт аккаунта, и если вы это делаете - вы скорее всего что-то делаете не так.
2. Нода возвращает результаты в JSON, но в JSON нет возможности передавать массив байт, поэтому в отличие от других типов данных (строк, чисел и булевых значений), они кодируются в `base64` представление. На самом деле, при записи данных типа массив байт в блокчейн с помощью `waves-transactions` он так же конвертирует байты в `base64` строку и отправляет это, а не массив байт в виде чисел или нулей и единиц. Вот, например, как выглядит сформированная транзация для отправки в API c помощью `POST` запроса:

`` 'json

{
  "type": 12,
  "version": 1,
  "senderPublicKey": "3SU7zKraQF8tQAF8Ho75MSVCBfirgaQviFXnseEw4PYg",
  "fee": 100000,
  "timestamp": 1592905798005,
  "proofs": [
    "KAQ9jhokgsZ8akvBbHjA8nDjR2PedkRvWZqf9ySRqjWi7dFXBpHga12CirpGeHJ3d4ATT92raZzqv2xbLetrCdZ"
  ],
  "id": "6wHqcdQeZpYWLxHj9nC7YpT7WBzpzNyrXxWZHxVCpViz",
  "data": [
    {
      "type": "integer",
      "key": "integerVal",
      "value": 1
    },
    {
      "type": "boolean",
      "key": "booleanVal",
      "value": true
    },
    {
      "type": "string",
      "key": "stringVal",
      "value": "hello"
    },
    {
      "type": "binary",
      "key": "binaryVal",
      "value": "base64:AQIDBA=="
    }
  ]
}

``

Будучи DevRel компании Waves, я получал много вопросов относительно потенциально некотролируемого роста размера блокчейна из-за хранилищ аккаунтов. Многих людей, особенно у которых есть опыт работы с другими блокчейнами, смущает факт возможности записывать много данных по фиксированной и достаточно низкой цене, а так же масштабируемость такого решения. В некоторых случаях (особенно долгосрочного хранения) блокчейн Waves может быть экономически выгоднее, чем хранение в Amazon S3, что потенциально опасно для масштабирования сети. Простого ответа на этот вопрос действительно нет, пока размер блокчейна Waves составляет порядка 40 гигабайт (не ~2.8 ТБ как в Ethereum), так что проблема не актуальна, зато простота записи позволяет делать "блокчейн для людей", о чем мы говорили в самом начале книги. Проблема станет актуальной только в случае быстрого роста популярности блокчейна Waves, но в таком случае будет расти и цена токенов, соответственно, стоимость хранилища тоже, что будет приводить к меньшему количеству желающих писать в блокчейн большие объемы данных. Там, где технология не могут полностью решить проблему, приходит на помощь экономика, что и будет происходить в случае роста популярности.

## SetScript транзакция (type = 13)

Транзакции типа `SetSсript` мы косвенно затрагивали, когда говорили про смарт-аккаунты. Логику поведения смарт-аккаунта и децентрализованных приложений мы описываем с помощью языка Ride, который компилируется в `base64` представление одним из доступных способов (JS бибиотека `ride-js`, API ноды, Java пакет в Maven, online IDE, плагин для Visual Studio Code или консольная утилита `Surfboard`) и отправляется в составе `SetScript` транзакции:

`` `js
const { setScript } = require('@waves/waves-transactions')
const seed = 'example seed phrase'
const params = {
  script: 'AQa3b8tH', // TRUE в base64 представлении
  // senderPublicKey: 'by default derived from seed',
  //timestamp: Date.now(),
  //fee: 100000,
  //chainId: 'W'
}

const signedSetScriptTx = setScript(params, seed)
broadcast(signedSetScriptTx);
``

`SetScript` транзакция используется только для аккаунтов, чтобы сделать из них Smart Account или децентрализованное приложение (dApp), но не для токенов. Установка скрипта с помощью `SetScript` транзакции меняет поведение аккаунта не только с точки зрения того, какие транзакции будут попадать в блокчейн, но и с точки зрения комиссии. Смарт-аккаунт платит на 0.004 Waves больше за каждый вид транзакции, по сравнению с обычным аккаунтом.

Чтобы превратить смарт-аккаунт в обычный аккаунт без скрита, необходимо отправить транзакцию `SetScript` с параметром `script` равным `null`. Но не каждый смарт-аккаунт может снова стать обычным аккаунтов. Скрипт смарт-аккаунта может прямо запрещать делать транзакцию `SetScript` или накладывать другие ограничения.

## SetSponsorship транзакция (type = 14)

Особенности спонсирования транзакций и пример SetSponsorship транзакции мы рассматривали в [главе 4](../../sections/4-Tokens/4-2-sponsorship.md), но давайте кратко вспомнил основную суть.

Создать токена имеет возможность отправить транзакцию, которая включает спонсирование транзакций с использованием этого токена в качестве комиссии. Пользователи будут платить комиссию в токене, но так как майнеры всегда получают комиссию только в Waves, то Waves будут списываться с аккаунта, выпустившего токен.

- Пользователь платит комиссию за транзакцию спонсируемым токеном (например, он отправляет 10 токенов, дополнительно платит 1 токен в виде комиссии, в итоге с его аккаунта списывается 11 токенов)
- Создатель токена получает комиссию в его токене (1 в нашем примере)
- С аккаунта создателя списываются WAVES в необходимом количестве и уходят майнерам (количество спонсируемых токенов и их соотетствие Waves настраивается в момент отправки транзакции SetSponsorship)

! [How sponsorship works] (../../ assets / 4-2-1-sponsorship.png "How sponsorship works")

Отправить транзакцию включения спонсирования можно достаточно просто:

`` `js
const {sponsorship} = require ('@ waves / waves-transactions')

const seed = 'example seed phrase'

const params = {
  assetId: 'A',
  minSponsoredAssetFee: 100
}

const signedSponsorshipTx = sponsorship (params, seed)
``

The code above will form (but not send to the blockchain) a transaction:

`` 'json
{
  "id": "A",
  "type": 14,
  "version": 1,
  "senderPublicKey": "3SU7zKraQF8tQAF8Ho75MSVCBfirgaQviFXnseEw4PYg",
  "minSponsoredAssetFee": 100,
  "assetId": "4uK8i4ThRGbehENwa6MxyLtxAjAo1Rj9fduborGExarC",
  "fee": 100000000,
  "timestamp": 1575034734209,
  "proofs": [
    "42vz3SxqxzSzNC7AdVY34fM7QvQLyJfYFv8EJmCgooAZ9Y69YDNDptMZcupYFdN7h3C1dz2z6keKT9znbVBrikyG"
  ]
}
``

Чтобы отменить спонсирование транзакций, достаточно отправить транзакцию c полем `minSponsoredAssetFee` равным `null`.

## SetAssetScript транзакция (type = 15)

Данная транзакция по своей сути похожа на `SetScript` транзакцию, за одним исключением - она позволяет менять скрипт для токена, а не аккаунта.

`` `js

const { setAssetScript } = require('@waves/waves-transactions')
const seed = 'example seed phrase'
const params = {
  script: 'AQa3b8tH', // TRUE в base64 представлении
  assetId: '4uK8i4ThRGbehENwa6MxyLtxAjAo1Rj9fduborGExarC',
  // senderPublicKey: 'by default derived from seed',
  //timestamp: Date.now(),
  //fee: 100000,
  //chainId: 'W'
}

const signedSetAssetScriptTx = setAssetScript(params, seed)
broadcast(signedSetAssetScriptTx);
``

`SetAssetScript` возможна только для ассетов, на которых уже есть скрипт. Если вы с помощью `Issue` транзакции выпустили токен, который не имеет скрипта, то установить на него скрипт в дальнейшем не удастся.

Установка скрипта на токен увеличивает минимальную комиссию для операций с этим токеном на 0.004 Waves (прямо как в случае со смарт-аккаунтами и децентрализованными приложениями).

Например, минимальная комиссия `Transfer` транзакции составляет 0.001, но для смарт-ассетов составляет 0.005 Waves. Если мы захотим сделать перевод смарт-ассета со смарт-аккаунта, то придется уже заплатить не менее 0.009 Waves (0.001 базовой стоимости, 0.004 прибавки за выполнение скрипта смарт-аккаунта/децентрализованного приложения и столько же за выполнение кода смарт-ассета).

## InvokeScript транзакция (type = 16)

`InvokeScript` транзакция является одной из самых важных транзакций в сети, так как она предназначена для вызова функций в децетрализованных приложениях.

`` `js
const { invokeScript } = require('@waves/waves-transactions')

const seed = 'example seed phrase'

const params = {

  call: {
    args: [{ type: 'integer', value: 1 }],
    args: [{ type: 'binary', value: 'base64:AAA=' }],
    args: [{ type: 'string', value: 'foo' }],
    args: [{ type: 'boolean', value: true }],
    function: 'foo',
  },
  payment: [
    {
      amount: 16,
      assetId: '73pu8pHFNpj9tmWuYjqnZ962tXzJvLGX86dxjZxGYhoK'},
    {
      amount: 10,
      assetId: null
    }
  ],
  dApp: '3Fb641A9hWy63K18KsBJwns64McmdEATgJd',
  chainId: 'W',
  fee: 500000,
  feeAssetId: '73pu8pHFNpj9tmWuYjqnZ962tXzJvLGX86dxjZxGYhoK',
  // senderPublicKey: 'by default derived from seed',
  //timestamp: Date.now(),
  //fee: 100000,
  //chainId:
}

const signedInvokeScriptTx = invokeScript(params, seed)
console.log(signedInvokeScriptTx)

``

Пример выше вызовет функцию `foo` децентрализованного приложения на аккаунте с адресом  `3Fb641A9hWy63K18KsBJwns64McmdEATgJd`. При вызове функции передаются 4 аргумента. Аргументы в `InvokeScript` не именованные, но их порядок должен совпадать с порядком, объявленным в коде децентрализованного приложения. `InvokeScript` позволяет так же прикрепить к вызову до 2 видов токенов в качестве платежа. В примере выше в качестве оплаты прикрепляются токен `73pu8pHFNpj9tmWuYjqnZ962tXzJvLGX86dxjZxGYhoK` и Waves (с `assetId = null`).

`InvokeScript` наряду с `Transfer` могут быть спонсированы, поэтому в примере выше вызов контракта оплачивается токеном `73pu8pHFNpj9tmWuYjqnZ962tXzJvLGX86dxjZxGYhoK`, который должен быть спонсированным.

При работе с некоторыми приложениями может возникать желание отправлять транзакции типа `InvokeScript` с больш**и**ми аргументами, но сделать это не получится, так как ограничение на размер всей транзакции составляет 5кб (включая все аргументы). Если функции в децентрализованном приложении надо передавать аргументы, которые больше этого ограничения, то возможен следующий сценарий:

1. Отправить `Data` транзакцию (до ~140кб данных)
2. При вызове функции с помощью `InvokeScript` передавать в качестве аргумента ключи, которые были записаны с помощью `Data` транзакции.
3. В коде децентрализованного приложения читать значения по переданным ключам и их обрабатывать.

## UpdateAssetInfo транзакция (type = 17) [stagenet]

Новая транзакция `UpdateAssetInfo` (type = 17) доступна только в сети Stagenet на момент написания этих строк. Она позволяет обновлять данные о выпущенном токене. В протоколе давно существует транзакция перевыпуска (`Reissue`), которая позволяет довыпустить токены и запретить перевыпуск в дальнейшем, но возможности изменить название или описание токена раньше не было.

Чтобы избежать недопониманий, давайте зафиксируем отличия транзакций перевыпуска (`Reissue`) и обновления информации (`UpdateAssetInfo`):

- `Reissue` позволяет довыпустить токен (количество задается создателем) и поменять флаг `reissuable` (только на `false`), если в момент выпуска токена создатель поставил `reissuable = true`.
- `UpdateAssetInfo` позволяет обновить название и описание токена, но не чаще, чем раз в 100 000 блоков.

`` `js
const { updateAssetInfo } = require('@waves/waves-transactions')
const seed = 'example seed phrase'
const params = {
  script: 'AQa3b8tH', // TRUE в base64 представлении
  assetId: '4uK8i4ThRGbehENwa6MxyLtxAjAo1Rj9fduborGExarC',
  description: "New description",
  name: "New name"
  // senderPublicKey: 'by default derived from seed',
  //timestamp: Date.now(),
  //fee: 100000,
  //chainId: 'W'
}

const updateAssetInfoTx = updateAssetInfo(params, seed)
broadcast(updateAssetInfoTx);
``

## Особенности работы с транзакциями

При формировании транзакций с использованием библиотек часто хочется указывать минимальное количество параметров, чтобы библиотека сама заполнила все остальные. Библиотека `waves-transactions` так и делает, предлагая заполнить только самые важные поля и подставляя остальные параметры по умолчанию. Однако существуют поля в библиотеке, заполнять которые самим не обязательно, но понимать их и знать об их существовании желательно.

### additionalFee

Для всех типов транзакций есть дополнительное поле `additionalFee`, который позволяет добавить дополнительную комиссию к значениям по умолчанию. Это может быть полезно в 2 случаях:

- Указать дополнительную комиссию при работе со смарт-ассетами и смарт-аккаунтами. Например, минимальная комиссия за `Transfer` транзакция по умолчанию составляет 0.001 Waves и именно это значение укажет библиотека `waves-transactions`, но в случае работы со смарт-ассетами необходимо дополнительно заплатить 0.004. Библиотека не знает, что транзакция отправляется с использованием смарт-ассета, поэтому разработчику необходимо самому предусматривать дополнительную комиссию. Конечно, можно использовать поле `fee`, чтобы указать всю комиссию целиком, но использование `additionalFee` удобнее, ведь не надо самому помнить минимальные комиссии за каждый тип транзакции.
- Отправлять транзация с повышенной комиссией для быстрого попадания в блок. Загрузка сети Waves сейчас сильно меньше пропускной способности, поэтому необходимость указывать повышенную комиссию встает крайне редко, но такая возможность существует. В следующей главе мы поговорим про сортировку транзакций в UTX (листе ожидания для попадания в блок) и вы поймете как размер комиссии влияет на скорость попадания в блок.

В таблице ниже представлены минимальные комиссии за транзакции разных типов (при отправке с обычного аккаунта и без взаимодействия со смарт-ассетами):

![Min transaction fees](../../assets/5-2-1-transaction-min-fees.png "Min transaction fees")

### chainId

В примерах транзакций выше вы могли замечать поле `chainId`, которое чаще всего было указано как `W`. Каждая транзакция в сети `Waves` содержит в себе байт сети либо в прямом виде, либо опосредованно (когда в транзакциях задействован адрес получателя). Байт сети мы рассматривали, когда говорили про адреса в [разделе 3](../../sections/3-Accounts/3-1-keys.md).

Байт сети - уникальный идентификатор сети, который позволяет отличать адреса и транзакции в разных сетях (mainnet, testnet, stagenet). Байты сети для перечисленных выше сетей - `W`, `T`, `S` соответственно. Благодаря байту сети невозможно ошибиться и отправить токены на адрес, которого не может существовать в этой сети, в которой отправляется транзакция. Если бы не было байта сети, то была бы возможна атака на пользователей, которые используют одну пару приватного и публичного ключей в нескольких сетях (stagenet и mainnet, например). Злоумышленник мог бы скопировать транзакцию из сети stagenet от пользователя и отправить ее в сеть mainnet, произведя действие, которое пользователь не хотел делать в mainnet. Благодаря байту сети такое невозможно.

### timestamp

У каждой транзакции есть время его создания, которое прописывается в транзакции и подписывается отправителем наряду с другими полями. `waves-transactions` по умолчанию поставит время, которое задано в оперциаонной системе, где запускается код. В протоколе Waves ноды синхронизируют время друг с другом с помощью протокола NTP, поэтому отличие между ними составляет не больше 1 секунды. Можно сказать, что сеть Waves знает актуальное время, и актуальное время прописывается в теле блока в момент его создания нодой. Если какой-либо генератор попытается сделать блок "из прошлого" или "из будущего", то остальные генераторы и валидаторы такой блок не примут.

Что же касается времени транзакции, то оно может отличаться от времени блока не более, чем на 90 минут в прошлом и 120 минут в будущем. Вы можете отправить транзакцию, в которой `timestamp` будет из будущего на 120 минут и генераторы попробуют добавить ее в блок, но если отправить со временем, которое больше времени на нодах на 121 минуту, то транзакция уже будет отвергнута.

Параметр `timestamp` может использоваться для регулирования сколько максимально времени транзакция может находиться в списке ожидания на попадание в блок. Если сеть загружена, транзакции попадают в блок очень медленно и нам не хочется платить большую комиссию, но мы готовы подождать, то можно поставить `timestamp`, который на 120 минут больше времени на нодах. Такая транзакция будет валидной в течение 210 минут (3 с половиной часа) и только если она не попала в блок в течение этого времени, она будет отвергнута. Может быть и обратная ситуация, когда нам важно, чтобы транзакция могла только быстро попасть в блок или не попасть вовсе. В таком случае, установка `timestamp` на 85 минут меньше, чем актуальное время, гарантирует, что она будет валидной только 5 минут, и если в течение этих 5 минут не попала в блок, то будет вычищена из UTX и уже никогда не попадет в блок.

При использовании поля `timestamp` транзакций в коде смарт-контрактов необходимо помнить, что оно может отличаться от настоящего на [-90; +120] минут. В разделе 7 мы поговорим о том, как правильно использовать время, если оно вам все-таки надо в коде контракта.

### proofs

Поле `proofs` является массивом, который предназначен для подписей транзакции. Подписей может быть до 8. На самом деле, в этом поле можно хранить не только подписи, но и использовать для передачи в качестве аргументов в смарт-аккауты или децентрализованные приложения. Это особенно может быть полезно при работе со смарт-аккаунтами, которые не могут принимать аргументы как функции.

### id

Каждая транзакция в сети имеет уникальный ID, который является хэшом на основе полей транзакции. В сети не может быть 2 одинаковых транзакций с двумя одинаковыми ID. ID транзакции вычисляется `waves-transactions` автоматически и оно может быть использовано для работы с API - для ожидания попададания в блок или проверки статуса.

### version

В сети Waves есть не только много разных типов транзакций, но могут быть несколько разных версий для каждого типа. Например, для типов вроде `Transfer` или `Issue` существуют 3 версии. Важно учитывать, что JSON представление транзакций при работе с API может отличаться для разных версий одного и того же типа.

## Signature of transactions

У каждой транзакции последних версий может быть не одна подпись, а до 8. В примерах выше мы всегда использовали сид фразу, из которой библиотека `waves-transactions` сама получала публичный ключ `senderPublicKey` и подпись в массив `proofs`. Бывают ситуации, когда отправить необходимо транзакцию с одного аккаунта, а подписать ключом другого. В таком случае, формировать транзакцию нужно с явным указанием `senderPublicKey` отправителя следующим образом:

`` `js
const { setScript } = require('@waves/waves-transactions')
const seed = 'example seed phrase'
const params = {
  script: 'AQa3b8tH', // TRUE в base64 представлении
  senderPublicKey: '4VStEwhXhsv6wQ6PBR5CfEYD8m91zYg2pcF7v17QGSbJ',
}

const signedSetScriptTx = setScript(params, seed)
``

Если же необходимо подписать несколькими ключами, то существует 2 варианта это сделать:

- использовать функцию `addProof(tx: ITransaction, seed: string)`, которая принимает тело сформированной транзакции и добавляет подпись от сида, передаваемого вторым аргументом
- при формировании транзакции передавать массив сид фраз

`` `js
const { setScript } = require('@waves/waves-transactions')
const seeds = ['0 - example seed phrase', '1 - example seed phrase', null, '3 - example seed phrase']
const params = {
  script: 'AQa3b8tH', // TRUE в base64 представлении
  senderPublicKey: '4VStEwhXhsv6wQ6PBR5CfEYD8m91zYg2pcF7v17QGSbJ',
}

const signedSetScriptTx = setScript(params, seeds)
``

В таком случае, созданная транзакция будет содержать 3 подписи в массиве `proofs` под индексами 0, 1 и 3, а под индексом 2 будет `null`:

`` 'json
{
  "type": 13,
  "version": 1,
  "senderPublicKey": "4VStEwhXhsv6wQ6PBR5CfEYD8m91zYg2pcF7v17QGSbJ",
  "chainId": 82,
  "fee": 1000000,
  "timestamp": 1587883659092,
  "proofs": [
    "4cajf7tKFJR2rvzWpsufytU1p1dTtstbnRLg1A89eCgg2ezFRqe1UKyux5vzK1BeFeoiGFpZ8Vu6epzFTdhZQqWe",
    "3PVzmWVnS2CJWpXDonCuWGgE48FsxZWQVriwNJXmstxZvqWQaowsebnAC5zca7j71cHQpZxB5yizmhzzKT9cvWXh",
    null,
    "2d6yyeTzjF5J8frSyuyBf3B2qKyoKuHEJq4X22joghjyeW7nZJBWdQhLVfxaUYQ6GnAhjXA7Mz7FXXkhRz7n5Zh9"
  ],
  "id": "8btD3NufMo8VApFi4opTPPdfa2ej6w2SFTojCaMcaqQq",
  "script": "base64:..."
}
``