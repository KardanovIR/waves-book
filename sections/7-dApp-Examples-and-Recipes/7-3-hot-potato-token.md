# Smart assets and where does the hot potato come in?

Smart assets are extremely powerful tools that, if used correctly, can allow you to quickly and easily implement restrictions on working with a token. Let's develop a token game called hot potato.

## What are potatoes and why are they hot?

You may have heard about the game [hot potato](https://en.wikipedia.org/wiki/Hot_potato), which appeared already in 1888, but if suddenly you haven't heard, I will briefly explain the rules. The participants of the game gather in a small circle and throw a small object to each other, in parallel with this some kind of music is played. At some point, the music stops playing and the player holding the item at that moment is out of the game. In the next round, everything starts over until there is only 1 player left.

## HotPotatoToken

Let's implement a token with similar mechanics:

- When a user receives a token, they have 5000 minutes to transfer it to someone else. After this period, the token can still be sent to someone, but only if the transaction fee is greater than 1 Waves. Or the token can be burned, but 5 Waves will have to be paid in the form of a commission.
- Since the generation of a new account does not cost anything, let's add a condition that you can send a "hot potato" only to an account that has more than 10 Waves on its balance
- User can only have one hot potato at a time
- All of the above restrictions do not apply to the account that issued the token

Let's declare the main variables for our script. Unlike a smart account, which can be implemented using the `@ Verifier` function, a smart asset must have an` EXPRESSION` code:

``` scala

{- # STDLIB_VERSION 4 # -}
{- # CONTENT_TYPE EXPRESSION # -}
{- # SCRIPT_TYPE ASSET # -}

# on the account receiving hot potatoes cannot be less than this amount
let minimumWavesBalance = 10_00_000_000

# The number of milliseconds during which the minimum commission will be standard (0.005 Waves from a regular account)
let moveTimeInMs = 5000 * 60 * 1000

# Commission for sending after the expiration of moveTimeInMs
let minimalFeeToMove = 1_00_000_000

# Commission for burning token
let minimalFeeToBurn = 5_00_000_000
```

You may have noticed the underscore in the numbers `_`, which is supported in Ride to make it easier to read. Eight zeros are separated from the rest of the digits, since Waves has 8 decimal places and it is easier to see the number of whole Waves tokens this way.

In the global scope, the variable `tx` is available, which stores information about the current transaction being processed. Since `tx` is` Union` from all possible transaction types, we need to use pattern matching in our code. We will have different conditions for `Tranfer`,` Burn` and all other types of transactions.

``` scala
match (tx) {
    # temporarily leave the check of Tranfer transactions in this form
    case t: TransferTransaction => {
        false
    }
    # We only allow burning if the commission is more than 5 Waves
    # or the sender is the account that issued the token
    case b: BurnTransaction => {
        if (b.fee> = minimalFeeToBurn || tx.senderPublicKey == this.issuerPublicKey) then true
        else {
            throw ("You have to pay 5 WAVES to burn this token")
        }
    }
    # all other types of transactions are allowed for the creator tokens and prohibited for all others
    case _ => if tx.senderPublicKey == this.issuerPublicKey then true else throw ("You only can transfer this token")
  }
```

In the code above, we have implemented a check that if the transaction performed with the token is `Burn`, then one of the conditions must be met - the sender is the account that issued this token or the commission is greater than the minimum value for burning (in our case, 5 Waves). We used the keyword `this`, which in the context of a smart asset denotes the type` Asset` and contains information about the current token.

> Please note that when verifying the sender of a transaction, we only check if the public keys match and do not verify that the correct signature from the given public key is provided, since ** the `proofs` array is not available in the smart asset script **. Signature verification is the responsibility of the account, not the asset.

In the script above, we have prohibited all `Transfer` transactions, but the logic of the" hot potato "implies that we should allow them if the token was received less than 5000 minutes ago or the commission is higher than 1 Waves.

Before we start writing the code, we need to understand how we will verify the fact that the token was received less than 5000 minutes ago. Unfortunately, there is no function in Ride that would allow us to find the moment of receiving the token. Moreover, there are practically no functions in Ride that allow you to look at the transaction history. We could require to provide the id of the transaction for receiving the potatoes in the `proofs` array, because as you remember, up to 8 arguments can be passed in` proofs`, but `proofs` is not available in the smart asset code.

The solution would be to require the id of the potato receipt transaction as an attachment to the Tranfer transaction.

``` scala
match (tx) {
    case t: TransferTransaction => {

        # If the sender of the token is the account that issued it, then no checks are needed
        if (this.issuerPublicKey == t.senderPublicKey) then true
        else {
            # to calculate how much time has passed since the token was received
            # attachment must contain the id of the receive transaction
            let txId = t.attachment

            # get the balance for the HotPotato token to check that the recipient no longer has a hot potato
            let currentRecipientHasPotato = assetBalance (t.recipient, t.assetId)> 0

            # get the balance of Waves tokens to make sure that the recipient's account has at least 10 Waves
            let currentRecipientWavesBalance = wavesBalance (t.recipient)

            # If the recipient already has a hot potato, then throw an exception
            if (currentRecipientHasPotato) then throw ("The recipient already has a hot potator")
            # If the user has less than 10 Waves, then throw an exception
            else if (currentRecipientWavesBalance <minimumWavesBalance) then throw ("Recipient is too poor") else {

                # Get information about the transaction from which the current sender received his potato
                let transaction = transferTransactionById (t.assetId.value ()). valueOrErrorMessage ("Can't find incoming transaction")

                # Get the block number in which the current sender received his potato
                let receivedBlockNumber = transactionHeightById (transaction.id) .valueOrErrorMessage ("Can't find incoming tx block number")

                # We get all the information about the block in which the current sender received his potato
                let receivedBlockTimestamp = blockInfoByHeight (receivedBlockNumber) .value (). timestamp

                # Verify that the user received their hot potatoes less than 5000 minutes ago
                let receivedAssetInLastNMs = (lastBlock.timestamp - receivedBlockTimestamp) <= moveTimeInMs && t.assetId == transaction.assetId

                # Check if the commission is more than 1 Waves
                let feeMore1Waves = t.fee> = minimalFeeToMove

                # If the transaction is received more than 5000 blocks ago AND the commission is less than 1 Waves
                # Throw an exception
                # Otherwise, allow the transaction
                if (! receivedAssetInLastNMs &&! feeMore1Waves) then {
                    throw ("You got potato long time ago, now you have to pay 1 WAVES fee")
                } else {
                    true
                }
            }
        }
    case burn: BurnTransaction => {...}
    case _ => ...
  }
```

We have implemented all the necessary functionality using several functions and variables of the standard library:

- `transferTransactionById (txId: ByteVector)` allows you to get information about the `Transfer` transaction by its Id. Anticipating questions, I must say right away that the same function does not exist for other types of transactions.
- `blockInfoByHeight (n: Int)` allows you to get information about the block by its number
- `lastBlock` - contains all information about the current (last) block

Smart assets can have much more interesting and florid mechanics and terms of use, but the hot potato example sufficiently describes what types of restrictions can be and how tokens can be used in your application.

One such application that harnesses the power of smart assets is the [Tokenomica](https://tokenomica.com/) project, which allows the issuance and investment of tokenized securities.