# Development best practices

As you may have already noticed, developing decentralized Web3 applications can be sometimes non-trivial, which differs from regular application development in many respects:

- **Error cost**. A bug in decentralized applications can often lead to loss of funds, sometimes loss of user funds
- **Open source**. Even if you do not want your code to be available to other users and developers, it will lie in the blockchain and there is always an opportunity to decompile it (in Waves, for example, this is done very simply even in the blockchain explorer)
- **A large number of interconnected elements**. Some decentralized applications can rely on the logic of others
- **Possible protocol updates**. For example, updating the Waves protocol to 1.2 and activating feature 14 (if adopted in the mainnet) completely change the logic of working with script call transactions. In fact, the environment of the application can change. It's hard to imagine this in a typical development.

Let's see what mistakes developers make most often and what needs to be done to avoid them.

## Always check the signature

One of the most common mistakes developers make is to use the `case _ => true` check construct in smart account scripts or the` @ Verifier` functions of decentralized applications. For example, you might think that the following script only forbids `Transfer` transactions and allows all others:

``` scala
{- # STDLIB_VERSION 3 # -}
{- # CONTENT_TYPE EXPRESSION # -}
{- # SCRIPT_TYPE ACCOUNT # -}

match (tx) {
    case t: TransferTransaction => false
    case _ => true
}
```

But the devil is in the details. Such a script completely prohibits making transactions from the `Transfer` account and allows all other types of transactions ** to any user **. Any person or even just a script can make a transaction from this account by specifying the public account key in the `senderPublicKey` field of the transaction and ** without specifying a single signature. **

**Always check** for signature and correctness:

``` scala
{- # STDLIB_VERSION 3 # -}
{- # CONTENT_TYPE EXPRESSION # -}
{- # SCRIPT_TYPE ACCOUNT # -}

match (tx) {
    case t: TransferTransaction => false
    case _ => sigVerify (tx.bodyBytes, tx.proofs [0], tx.senderPublikey)
}
```

You need to be careful not only in mainnet, but also in testnet, because both there and there there are scripts that watch all transactions in the blockchain and if they find accounts with such a vulnerability, withdraw all tokens from the account.

## Understand the difference between @Verifier and @Callable

Some decentralized application developers make a mistake when designing their dApp by mistakenly saying that `@ Verifier` checks **incoming** transactions against the dApp's address. For example, there are such scripts:

``` scala
@Callable (i)
func foo () = {
    [StringEntry ("foo", "bar")]
}

@Verifier (tx)
func verify = {
    match (tx) {
        case i: InvokeScriptTransaction => true
        case _ => false
    }
}
```

But such a script is not engaged in the fact that it allows you to call the methods of this dApp, but **allows you to call other dApps from the account of this decentralized application even without providing a signature.** That is, any user can call another account and transfer all tokens from the account of this application. Do not forget that the account of the decentralized application remains an account that can also do some actions and send transactions, and these actions are controlled by the `@ Verifier` function.

## Verify transactions before submitting

Script call transactions can end with an error, but earlier such transactions simply did not get into the blockchain, with the release of Waves 1.2 (at the time of this writing, only in stagenet) this situation has changed. Now `InvokeScript` transactions and transactions related to the use of smart assets get into the blockchain even if they return an error, and the user pays a commission for them.

It is impossible to make sure by 146% that the transaction will be fully completed and get into the blockchain, since the state of the blockchain changes quickly enough, new transactions appear in UTX, get into blocks and can change the branch that the script will follow. You can maximize the likelihood that a transaction will succeed using pre-validation. The REST API of the node has a method [`debug / validate`](https://nodes.wavesplatform.com/api-docs/index.html#/debug/validate), which accepts a transaction and validates it. The method returns what the result of the transaction script would be if it was added to the block right now.

Use this method for pre-validation before sending the transaction using the `broadcast` method.

> Important: this API method requires a key that cannot be obtained for public nodes, so use a node whose API Key you know.

## Be careful with keys

In the development of decentralized applications, many operations are performed with the account's key-value storage. Keys in the vault are often composite, for example, `voting_12_vote_3MEEsWQtsS5WV2SczdEvipY3Ch5LuSHuLWa`, which can store the vote of the account` 3MEEsWQtsS5WV2SczdEvipY3Ch5LuSHuLWa 'in voting with id. Formation of a key for such a record in the storage can be implemented in Ride as follows:

``` scala
func keyVoteByAddress (votingId: Int, address: String) = "voting_" + votingId + "_vote_" + address
```

A common mistake is that they make a mistake in forming a key: they write to one key, and try to read from another key. For example, forget the `_` character in one of the places. To avoid this mistake, always use separate key generation functions instead of our favorite copy & paste behavior. And of course, write tests for your applications.

## Use default values

Another common mistake, including with keys in storage, is trying to read values ​​from variables of type `Union (T | Unit)` using `value ()` or `extract ()` in places where one could use default values. For example, if a function tries to read the user's voice from the storage, but there may be a situation that there is no voice yet, use the `valueOrElse` function or pattern matching:

``` scala

let NONE = "NONE"

func keyVoteByAddress (votingId: Int, address: String) = "voting_" + votingId + "_vote_" + address


@Callable (i)
func vote (id: Int) => {
    let voteKey = keyVoteByAddress (id, i.caller.toBase58String ())
    let vote = getString (this, voteKey) .valueOrElse (NONE)

    # Alternative option

    let vote = match getString (this, voteKey) {
        case s: String => s
        case _ => NONE
    }

    if (vote == NONE) then ...
    else ...
}
```

It is also worth considering that the functions of your application can be called not only from your user interface, but by anyone and in any way, so the default values ​​can help them too.

## Control your transactions

In the work of real decentralized applications, there are relatively often cases when it is necessary to execute several dependent transactions sequentially. For example, if you use the [commit-disclosure](https://en.wikipedia.org/wiki/Commitment_scheme) scheme, then the disclosure phase can only be after the commit phase. If you send a disclosure transaction before the commit transaction hits the blockchain, then your script will return an error, the user will pay a fee and not get the expected result.

In the Waves blockchain, there may be rare situations when a fork occurs in the blockchain and the last block or microblock is rolled back, which can lead to disruption of the sequence of dependent transactions. For example, if you send a transaction for the commit phase, wait until it gets into the last (liquid) block and immediately send the disclosure transaction, then there may be a situation when the last block or microblock rolls back, the commit transaction "falls out" of the blockchain. This will make the transaction invalid for the disclosure phase.

If you use the `waitForTx` function from the` waves-transactions` library, then it waits only to hit the last fluid block, which can lead to problems. If you have dependent transactions, it is safer to use the `waitForTxWithNConfirmations` function, waiting for 1-2 confirmations after the first transaction hits the block.