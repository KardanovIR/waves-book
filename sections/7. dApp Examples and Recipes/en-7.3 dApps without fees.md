# How to: free trial peroid for your dApp

There are several topics among decentralized application developers, discussions of which lead to a visible pain on their faces. The topics are:

1. **Account keys handling.** Developers cannot ask user for their private keys/seed phrases, but the user always has to sign transactions.
2. **The need to pay fees for each transaction.** How to explain to users that each transaction requires a fee in the platform token, and more important - where will the users get tokens for their first transactions?

The indicated problems lead to a very high acquisition cost per user. For example, one of the popular dApps in the Waves ecosystem had a customer acquisition cost of about $80 (!), with a lite-time-value (LTV) less than $10. The conversion was spoiled by barriers with two topics described above.

The first problem is often solved with browser extensions like Metamask and [Waves Keeper](https://wavesplatform.com/technology/keeper), but this solution is not user-friendly and requires a lot of effort, so [Signer](https://docs.wavesplatform.com/en/building-apps/waves-api-and-sdk/client-libraries/signer) appeared in the Waves ecosystem. It does not force the installation of browser extensions. An [article](https://medium.com/@izhur27/getting-started-with-waves-signer-893017c9b7ae) by @Vladimir Zhuravlev talks about this and how to integrate Waves Signer into your application.

What about the second problem? Many dApp creators simply do not care about this issue, users have to get tokens for fees from somewhere. Others require users to add plastic cards during registration/authorization, which reduces conversion.

**I will show you how to solve the problem with fees** and how to make a dApp that does not require a native token from user. This allows you to do trial periods or free first interactions. There are 2 ways to do this in Waves, we will cover both.

## Sponsored transactions

If you have your token that users of your dApp need, then you can use the [transaction sponsoring mechanism](https://docs.wavesplatform.com/en/blockchain/waves-protocol/sponsored-fee). Users will pay fees in your token, but since miners always receive fees only in Waves, in fact Waves will be debited from the account that issued the token. Let's take the steps again, as this is important to understand:

- The user pays a transaction fee in your custom token
- You as an issuer get these tokens
- WAVES tokens are deducted from your account in the required amount and goes to miners

*The question that should have arisen right away - how many tokens will the user pay and how many tokens will be debited from the sponsor's account?*

Answer: the owner can set the ratio himself. At the time of sponsorship, the creator of the token sets how many of his tokens correspond to the minimum fee (0.001 Waves or 100,000 in the minimum fraction). Let's move on to examples and code to make it clearer.

To enable sponsorship, you must send a transaction called `Sponsorship`. You can do it using UI in Waves.Exchange, or you can sent manually/with code using [waves-transactions](https://github.com/wavesplatform/waves-transactions). Уou can execute the following code:

```js
const { sponsorship } = require('@waves/waves-transactions')

const seed = 'example seed phrase'

const params = {
  assetId: '4uK8i4ThRGbehENwa6MxyLtxAjAo1Rj9fduborGExarC',
  minSponsoredAssetFee: 100
}

const signedSponsorshipTx = sponsorship(params, seed)
```

The code above will create (but not send to the blockchain) a transaction:

```json
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
```

The most important parameter in the transaction is `minSponsoredAssetFee`, which sets the correspondence of 100 `A` tokens to 0.001 Waves. Thus, in order to send `Transfer`, the user will have to attach 100 `A` tokens as a fee.

It is important to realize some of the limitations of sponsorship. Sponsored tokens can only be used as fees for `Transfer` and `InvokeScript` transaction types. Only the account that issued a token can sponsor it. You will not be able to sponsor tokens issued by other accounts.

### Security notes

Before you enable sponsorship, you must understand a few important notes.

1. The user can use sponsored tokens for transactions not only with this token. For example, an account with `A` and `B` tokens on the balance can send `B` tokens, and attach `A` tokens as fees.
2. The user may not pay a minimum transaction fee. For example, if a user has 100,000 of your tokens, and you set the `minSponsoredAssetFee` parameter to 100, then the user will be able to specify all of his 100,000 tokens as a fee. You will receive 100,000 `A` tokens, and the miner will receive 1000 Waves from your account (100,000/100 = 1000).

## Fees due dApp

**Disclaimer: this feature is NOT documented and may stop working in the future.**

The second option is working only for `InvokeScript` transactions and is a pretty simple. The advantage over sponsoring tokens is the lack of the need to use your token.

Any user could call dApps' functions with 0 Waves on their account. The transaction will succeed if dApp transfers Waves tokens as a result of invocation. Let's take an example, it’s always easier after all.

For example, there is dApp, which, sends or not 1 Waves to its' caller based on his address. **This example is completely unsafe and is provided only for explanation purposes. Never use this logic in your decentralized applications.**

```scala
@Callable(invoke)
func callMeBaby() = {

    let callerAddress = invoke.caller.bytes.toBase58String()

    if (takeRight(invoke.caller.bytes, 4) == base16'ABCD') then
        TransferSet([
            ScriptTransfer(invoke.caller, 100000000, unit)
        ])
    else
    throw("You didn't win")
}
```

If you call this function by an account with 0 Waves balance, specifying `fee` equal to 0.005 Waves, the script will run. If the condition `takeRight(invoke.caller.bytes, 4) == base16'ABCD'` will return true, then the transaction will be considered valid and will go into the blockchain. The user will receive 1 Waves as a result of the call first, then 0.005 fee will be deducted from his balance.

As a result, the user will have 0.995 Waves on his account.

Attentive readers have already guessed that the script above is unsafe, because you can call it as many as you want - if the account does not "win" 1 Waves, then the transaction will not go to the blockchain, if the account "won", it will receive 0.995 Waves. A win-win lottery turned out.

The correct recipe is to always check additional conditions whether the user has the right to call the script for free or not, for example, to have a white-list.

```scala
@Callable(invoke)
func callMeBaby(uuid: String) = {

    if (isInWhiteList(invoke.caller) && invoke.fee == 500000) then {

        let id = extract(invoke.transactionId)
        let callerAddress = toBase58String(invoke.caller.bytes)

        ScriptResult(
            WriteSet([
                DataEntry(toBase58String(id), callerAddress + uuid)
            ]),
            TransferSet([
                ScriptTransfer(addressFromStringValue(callerAddress), invoke.fee, unit)
            ])
        )
    }
    else
        throw()
}
```

I'd like to note additionally that if in the future the contract execution model in Waves changes and transactions with exceptions (`throw ()`) are also will be written to the blockchain, then the use of this functionality will become impossible.

A discussion of how the functionality described above works and possible development paths can be found in this [issue on github](https://github.com/wavesplatform/Waves/issues/2725).

## Best practics

Both solutions are recommended to be used only for trial modes of products. As a developer you always have to check all boundary conditions, because improper use can lead to loss of funds.
