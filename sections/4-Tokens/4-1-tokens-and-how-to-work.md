# Chapter 4. Tokens

After we talked about accounts, it is quite logical to talk about another important entity in the Waves blockchain - tokens. For me personally, it seems necessary to start with the background, because many people still know Waves as a platform for issuing tokens. During the ICO boom era (2017), Waves was the second most popular platform for issuing tokens, because it made it very easy and simple. In the first place was Ethereum, in which a smart contract must be written in order to issue tokens (simple and most often in accordance with ERC-20, but still). In Waves, issuing a token is very simple - sending one transaction of a special type `Issue`.

In some Waves clients (for example, Waves.Exchange), it is enough to fill out one small form to issue a regular token, which will automatically be available for transfers between accounts, work with decentralized applications, or trade on the DEX decentralized exchange. At the moment, more than 20,000 different types of tokens have been issued on the Waves blockchain.

## How tokens work

In Waves, all tokens are "first-class citizens", they are right in the core of the blockchain, as there are, for example, accounts. Some (especially those with experience with Ethereum) are surprised by this, but this approach has several advantages:

- **Ease of release.** In 2017, Waves ranked second precisely because of the ease of release, that there was no need to understand how Solidity or Ethereum Virtual Machine works to make a token, peg to some assets in real life, and start use.
- **Fast work.** Simple tokens in Waves do not execute any smart contract code for their work, so they will work faster than in the case of Solidity. In fact, a simple token in Waves is an entry in the LevelDB database.
- **Ability to trade out of the box.** Issued tokens on Waves are automatically supported for trading on decentralized exchanges based on the Waves Matcher. We will talk about the work of the matcher separately.

> *You should already have a question, what if I want to not just issue a token, but make my own logic for it?*

**This is also possible by creating smart assets, which we will look at later in this chapter.**

A lot of new developers are asking how a **asset** differs from a ** token **. In the code of the Waves node, you will more often come across the word `asset` than` token`, but for convenience, for the sake of this book, I suggest that these two concepts be considered interchangeable. Yes, in real life, `asset` is more of an asset, and` token` is more like something close to a token / coin, but in the world of blockchain the border between the concepts has become blurred.

Waves has only one token that is not an asset - the Waves token itself, which is paid as a commission to miners. We can say that all tokens in Waves are equal in capabilities, but the Waves token is a little "more equal" and its behavior differs from other assets.

## Token Issue

As I wrote above, to issue a token, it is enough to send a transaction of type `Issue`, which can be easily done using a JavaScript library:

```js

const {issue} = require ('@ waves / waves-transactions')

const seed = 'seed phrase of fifteen words'

const params = {
  name: 'Euro',
  description: 'It is an example of token',
  quantity: 1000000,
  // senderPublicKey: 'by default derived from seed',
  reissuable: false,
  decimals: 2,
  script: null,
  timestamp: Date.now (),
  fee: 100000000,
}

const signedIssueTx = issue (params, seed)
console.log (signedIssueTx)

```

As a result of executing this code, the following JSON transaction object will be displayed in the console:

``` 'json
{
  "id": "CZw4KCpPUv5t1Uym3rLc9yEaQyDsP3VVPspdpmWKvVPE",
  "type": 3,
  "version": 2,
  "senderPublicKey": "HRQUmzJKgHDGbsfS23kSA1VRuudy5MY3wGCroUmNhKuJ",
  "name": "Euro",
  "description": "It is an example of token",
  "quantity": 1000000,
  "decimals": 2,
  "reissuable": false,
  "script": null,
  "fee": 100000000,
  "timestamp": 1575034734086,
  "chainId": 87,
  "proofs": [
    "2ELbuezHiaHUDCuWpfhULwqSA8SUm4vGzWQe5QUmLEPZTA5WMXctiaXaoF9aUbr8TBSBreQxa8WYMsp6Sy2qSSGU"
  ]
}
```

Let's take a look at the main parameters:

- **name** - token name (4-16 bytes)
- **description** - description for the token (0-1000 bytes)
- **quantity** - the number of tokens issued
- **decimals** - number of decimal places. Please note that if you set the value to `0`, then the token will be indivisible. In the example above, the minimum unit will not be 1 token, but one hundredth of the token, which is logical, since we are issuing an analogue of the euro as an example. To be more precise, we issue 1 million tokens under the name `Euro`, the minimum unit of which will be the euro cent.
- **reissuable** - token reissuable flag. If the value is `true`, then the owner of the token at any time can add as many new tokens of this type as he wants. At the time of re-release, the owner can change the value of this flag, thus fixing the number of tokens in the blockchain.
- **script** - compiled Ride script that describes the logic of the token. In our example, the value is `null`, since we don't want to set any rules for token circulation.
- **fee** - commission for issuing a token. At Waves, the minimum transaction for issuing a regular token (not NFT) is 1 Waves. Why, then, is 100,000,000 indicated in the transaction, where there are already 8 zeros? It's simple, the Waves token has 8 decimals, and the commission is indicated in the smallest units, in the case of Waves, the minimum units are sometimes called ***waveslet***.

By sending such a signed transaction, you can create a new token called `Euro`. Of course, there is no value in such a token, but value is the next question. The newly created token will receive a unique identifier `assetId` equal to the ID of the transaction that generated it, in our case` CZw4KCpPUv5t1Uym3rLc9yEaQyDsP3VVPspdpmWKvVPE`.

This rule can be extremely useful, so I propose to remember - the `assetId` of the token is equal to the` ID` issue of the transaction that created it. In the future, when working with this token, in the overwhelming majority of cases, you will have to use its assetId, and not its name.

Another important parameter to remember is that the Waves token (native / system for paying transaction fees) does not have an `assetId`, so in places where a long string is inserted for other tokens, you need to set` null` for Waves.

## Issue NFT token

Non-fungible tokens are very often used for various mechanics, most often gaming. NFTs differ in that each token is unique and has its own unique identifier.

> The easiest way to explain the essence of NFT is with a simple analogy. For example, if you take one coin, put it in a bag with 100 of the same coins and mix the bag, then you cannot later determine which coin you put in the bag last. It would be another matter if the coins had numbers. NFT are coins with a unique identifier (number), when they can never be confused with others of the same.

In Waves, the issuance of Non-fungible tokens is carried out in the same way as the issuance of fungile tokens, but with several restrictions:

- `quantity` must be equal to one
- `decimals` must always be 0
- `reissuable` must be set to` false`

If the conditions above are met, it is already possible to issue a token with a commission not in 1 Waves, but a thousand times less - 0.001 Waves. For convenient work with NFT tokens, there is a JavaScript library `@ waves / waves-games`, which simplifies the creation and storage of meta-information about the token. An example of NFT release using this library can be found below:

```js

import {wavesItemsApi} from '@ waves / waves-games'
const seed = 'my secret backend seed'

const items = wavesItemsApi ('T') // testnet, use 'W' for mainnet
const item = await items
  .createItem ({
    version: 1,
    quantity: 100,
    name: 'The sword of pain',
    imageUrl: 'https://i.pinimg.com/originals/02/c0/46/02c046b9ec76ebb3061515df8cb9f118.jpg',
    misc: {
      damage: 22,
      power: 13,
    },
  }). broadcast (seed)
console.log (item)

```

Note that in the example above, 100 tokens are issued, not a token with a quantity of 100, but 100 different ones, each of which will have a unique ID. In other words, the library will send 100 issue transactions. The minimum commission for each token will be 0.001 Waves, and for all 100 - 0.1 Waves. You can find more examples on working with the library for NFT in [their tutorials](https://docs.item.market/sdk/lib.html#installation).

## Token re-issue

If the token at the time of creation had the value `true` for the` reissuable` field, then the creator can send transactions of the `Reissue` type, which will allow additional tokens to be issued. The example of generating a reissue transaction is very similar to the example from the issue:

```js

const {reissue} = require ('@ waves / waves-transactions')

const seed = 'example seed phrase'

const params = {
  quantity: 1000000,
  assetId: 'CZw4KCpPUv5t1Uym3rLc9yEaQyDsP3VVPspdpmWKvVPE',
  reissuable: false
}

const signedReissueTx = reissue (params, seed)

```

The main difference is that we cannot change the title or description. In our example, we add another million to the already issued one million Euro tokens.

You may also notice that this transaction also has a `reissuable` flag. If you send a reissue transaction with the `reissuable` field equal to` false`, then it will no longer be possible to send such reissue transactions for this token in the future.

In the example above, the field for the commission (`fee`) is omitted, but the library` @ waves / waves-transactions` will automatically substitute the minimum value in 1 Waves. I often write the "minimum value" of the commission to show that this value can be increased, but the Waves network is not experiencing any bandwidth problems right now, so even transactions with the lowest commission almost instantly fall into blocks.

Please note that there was a bug in the history of Waves for a short time, which allowed reissuing tokens for which the `reissuable` was` false`. The bug was promptly fixed, but some tokens may be embedded in the blockchain, which were always unreleased, but were reissued. It will not be possible to remove them from there, because the blockchain is immutable. So it's only worth knowing about this if you suddenly do an explorer or some kind of analytics.

## Burning tokens

Sometimes it happens that a token gets in the way and you don't want to see it in your portfolio, but very often you need to burn it according to some business logic. For this, Waves has a `Burn` transaction that allows you to burn tokens (but only from your account, of course).

```js

const {burn} = require ('@ waves / waves-transactions')

const seed = 'example seed phrase'

const params = {
  assetId: 'CZw4KCpPUv5t1Uym3rLc9yEaQyDsP3VVPspdpmWKvVPE',
  quantity: 100
}

const signedBurnTx = burn (params, seed)

```

The burn transaction is as simple as possible and allows you to set the assetId of the token that you want to burn and the amount. Actually, that's all.

## Changing token information

Many users ask if it is possible to change the name or description of their token. There can be many reasons for this - renaming the company, changing the website address (the website address could be in the token description). Until recently, such a change was impossible and the name and description set at the very beginning of life were forever with the token, but at the beginning of 2020 the `UpdateAssetInfo` transaction appeared, which allows you to update the name and description, but not more often than once every 100,000 blocks. which is about 2.5 months. At the time of this writing, the functionality was activated only in stagenet and the `UpdateAssetInfo` transaction was not yet supported by the libraries.

## What next?

The release of a token in most cases is just the beginning of the integration, so in the future we will talk about how to use Ride to define the logic and API for integrating your business logic with the blockchain.