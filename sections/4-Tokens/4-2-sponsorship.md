# Sponsoring transactions

There are several topics among developers of decentralized applications, the discussion of which leads to pronounced pain on the faces. These topics are:

1. **Working with keys.** You cannot ask the user for keys, but he must somehow sign transactions.
2. **The need to pay a commission in tokens for each transaction**. How to explain to users that each transaction requires a commission in the platform token, and what is equally important - where will they get commissions for their first transactions?

These problems lead to a very high cost of attracting one user. For example, one of the popular dApps in the Waves ecosystem had a customer acquisition cost of about $ 80 (!), With LTV (Lifetime Value or customer profit for all time) less than $ 10. The conversion was spoiled by the barriers with the need to install an extension for working with keys and commission.

The first problem is often solved with browser extensions like Metamask and Waves Keeper, but this solution is not user friendly and requires a lot of effort, which is why Signer appeared in the Waves ecosystem. It does not require dApp keys to be provided, and at the same time does not force browser extensions to be installed. See [@Vladimir Zhuravlev's article](https://medium.com/@izhur27/getting-started-with-waves-signer-893017c9b7ae) about this and how to integrate Waves Signer into your application.

What about the second problem? Many dApp creators simply do not care about this issue, users have to get tokens for commissions from somewhere. Others require you to bind your bank cards during registration, which greatly reduces motivation.

**Now I will tell you how to solve the problem with fees**. How can you create such an application on the blockchain that does not require a user to have a native token? This would allow, for example, to make trial periods for your projects on the blockchain!

## How sponsoring works

If you have your own token that the users of your application need, then you can use the [Waves transaction sponsor mechanism](https://docs.wavesplatform.com/en/blockchain/waves-protocol/sponsored-fee). Users will pay a commission in your token, but since miners always receive commission only in Waves, Waves will actually be debited from the account that issued this token. Let's take the steps again, as it is important to understand:

- The user pays a commission for a transaction in your token (for example, he sends 10 of your tokens, additionally pays 1 token in the form of a commission, as a result, 11 tokens are debited from his account)
- You receive these tokens (1 in our example), since it was you who issued the token
- WAVES are debited from your account in the required amount and go to miners (the number of sponsored tokens and their correspondence Waves is configured by sending a special transaction `SetSponsorship`)

![How sponsorship works](../../assets/4-2-1-sponsorship.png "How sponsorship works")

> *A question that should have immediately arisen - how many tokens will the user pay and how many tokens will be debited from the sponsor's account?*

Answer: the owner can set the ratio himself. At the start of sponsoring, the creator of the token sets how many of his tokens correspond to the minimum commission (0.001 Waves or 100,000 in the minimum fraction). Let's move on to examples and code to make it clearer.

To enable sponsorship, you need to send a transaction of type `Sponsorship`. Using the UI this can be done in Waves.Exchange, and using [waves-transactions](https://github.com/wavesplatform/waves-transactions) the following code can be executed:

```js
const {sponsorship} = require ('@ waves / waves-transactions')

const seed = 'example seed phrase'

const params = {
  assetId: '...',
  minSponsoredAssetFee: 100
}

const signedSponsorshipTx = sponsorship (params, seed)
```

The code above will form (but not send to the blockchain) a transaction:

``` 'json
{
  "id": "...",
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
```

The most important parameter in the transaction is `minSponsoredAssetFee`, which sets the correspondence that 100` A` tokens are equal to 0.001 Waves. Thus, to send a `Transfer` transaction, the user will have to attach 100` A` tokens as a commission.

It is important to understand some of the limitations associated with sponsoring. You can use sponsored tokens as a commission only for transactions of types `Transfer` and` Invoke`. Only the account that issued this token can sponsor a token. That is, you will not be able to sponsor tokens not issued by you. As soon as the balance of the token creator becomes less than 1.005 Waves, sponsorship will automatically turn off (and turn back on when the balance becomes more than this value again).

### Safety

Before including sponsorship, there are several important points to understand.

1. The user can use sponsored tokens for transactions not only with this token. For example, an account with `A` tokens on the balance can send` B` tokens, and apply `A` tokens as a commission.
2. The user can pay no minimum transaction fee. For example, if a user has 100,000 of your tokens, and you set the `minSponsoredAssetFee` parameter to 100, then the user will be able to specify all his 100,000 tokens as a commission for 1 transaction. You will receive 100,000 `A` tokens, and the miner will receive 1000 Waves from your account (100,000 / 100 = 1000), if you have them on your account.

The sponsorship feature has been in Waves for a long time and works great, but there is a [WEP-2 Customizable Sponsorship](https://forum.wavesplatform.com/t/wep-2-customizable-sponsorship/15880) that has ideas for it. improvement. If you have anything to add - join the discussion on the forum.