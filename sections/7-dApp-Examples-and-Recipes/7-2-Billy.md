# Billy

The second project that we will consider as part of our introduction to Ride is the Billy application.

Billy is a decentralized application (dApp) in the form of a bot for the corporate messenger Slack. You can read more about how Billy works on the official website of the project - [https://iambilly.app](https://iambilly.app). And here I will briefly tell you what parts Billy is made of and how exactly the blockchain is used in it.

Billy is a project to motivate company employees. In the course of work, situations often arise when we help colleagues or they help us. And it is far from always that such assistance is the direct responsibility of colleagues. To encourage colleagues to help each other, you can add Billy to Slack with one button on the project site.

Billy generates a unique address for each employee and stores it in the database. Every month the bot credits 500 “Thank you” tokens to the generated addresses, which can be spent using the same bot. To send tokens, company employees can use special commands (`10 thanks @ username`) or simply respond to messages using special emoji.

![Emoji reaction](../../assets/7-2-1-emoji.png "Emoji reaction")

The unused balance of 500 automatically accrued “Thank you” tokens expires at the end of the month. The tokens received from colleagues do not burn out and can be used in three ways:

- **Transfer to other users as a thank you.** Received tokens can be transferred to colleagues at any time
- **Purchase of goods in the internal store.** The internal store of the company allows its employees (and / or other authorized persons) to offer goods and services in exchange for thank you tokens
- **Participation in polls and crowdfunding campaigns.** Any user can indicate the purpose for which he collects tokens (for example, holding an internal meetup). Also, the company's management can initiate voting, in which each token will be considered one vote, which means that a more "useful" and active employee will have a greater impact on the voting results. In addition to the required number of tokens, each crowdfunding campaign has an expiration date and implementation period. The voting mechanism is similar to the DAO - a decentralized autonomous organization.

A video demonstration of the bot's work can be found on the [project website](https://iambilly.app). The system hides everything related to the blockchain for users as much as possible. If necessary, the user can request his seed phrase and use tokens outside of Slack, however, by default, in order not to create unnecessary barriers, all implementation details are hidden from him.

> **Billy is free for all companies, so it's best to understand its mechanics very quickly and simply: add to Slack and start using.**

## What's "under the hood"?

Before moving on to the implementation of the idea, let's describe all the requirements for entities in the blockchain in a more formal way:

1. A unique token is issued for each team in Slack, which will be the team's internal currency
2. A separate account is created for each team member, where only the tokens of this team are stored
3. Team tokens can only be transferred between team members, so the list of team members' addresses must be stored somewhere
4. Any team member can "burn" their tokens
5. Participation in the vote is carried out through calls to dApp functions with attached team tokens
6. In each vote, the user receives a unique NFT token, which confirms his vote.

The system is complex and must include several scripts. The first step - issuing a token to the team - is a fairly straightforward operation of sending the `Issue` of a transaction (we looked at the example in Chapter 5). We do not want users of the system to buy WAVES to pay commission for transfers. Therefore, we use the sponsorship function. This will allow users to pay for transactions in Thank You tokens, and WAVES fees will be deducted from the team admin account.

``` js
    const adminSeed = '...';
    const issueTx = issue ({
        name: `Thanks`,
        description: 'Say thank you to all of your teammates in Slack. By Billy. ',
        decimals: 0,
        quantity: 100000000,
        reissuable: false
    }, adminSeed);

    await broadcast (issueTx);
    await waitForTx (issueTx.id);
    const sponsorshipTx = sponsorship ({
        assetId: issueTx.id,
        minSponsoredAssetFee: 1
    }, adminSeed);
    await broadcast (sponsorshipTx);
```

We use the `waitForTx` function from the `waves-transactions` library to make sure that the token issuance transaction hits the blockchain, and only then send the sponsoring transaction.

To some extent, the use of sponsorship limits the fulfillment of other requirements: sponsorship cannot be used in conjunction with smart assets. That is, at the token level, we cannot impose a restriction on the transfer of tokens only to other team members. In the future, with the implementation of the proposal [WEP-2 Customizable sponsorship](https://forum.wavesplatform.com/t/wep-2-customizable-sponsorship/15880) it will become possible to enable sponsorship for smart assets. Until then, we can find another solution. For example, a script can be placed on the account of each team member (making it a smart account) that will check if the recipient of tokens is in the list of colleagues and, accordingly, approve or deny the transaction.

The list of team members' addresses must also be stored on the blockchain. Since we have a decentralized app for shopping and voting, we can add a feature to manage the list of team members.

Let's create a decentralized application that allows you to add or remove a person from the list. The add to list function will take an address as an argument and add a key-value pair to the store, where the key is the address and the value is `true`. The delete function will update the record in the repository and translate the value for the specified address to `false`.

``` scala

{- # STDLIB_VERSION 4 # -}
{- # CONTENT_TYPE DAPP # -}
{- # SCRIPT_TYPE ACCOUNT # -}

let adminPublicKey = base58 '...'

func addToWhiteList (address: String) = {
    let userInTheList = getBoolean (this, address)
    let newValue = match userInTheList {
        case b: Boolean => {
            if b then throw ("User is already in the list and enabled")
            else true
        }
        case _ => true
    }
    if i.callerPublicKey! = adminPublicKey then throw ("Only admin can call this function") else
    [
        [BooleanEntry (address, newValue)]
    ]
}

@Callable (i)
func removeFromWhiteList (address: String) = {
    if i.callerPublicKey! = adminPublicKey then throw ("Only admin can call this function")
    else [BooleanEntry (address, false)]
}
```

Let's write the smart account code that will be installed for each team member:

``` scala
{- # STDLIB_VERSION 4 # -}
{- # CONTENT_TYPE EXPRESSION # -}
{- # SCRIPT_TYPE ACCOUNT # -}

# specify the id of the token that we issued for this command
let assetId = base58 '...'
# address of the account that will host the decentralized application and a list of team members' accounts
let whiteListAddress = "..."

match tx {
    # Any team member can burn their tokens
    case b: BurnTransaction => {
        sigVerify (tx.bodyBytes, tx.proofs [0], tx.senderPublicKey)
    }

    # Updating this script is possible if there are 2 signatures - a team member
    # and the team administrator, from whose account the token for the team is issued
    case s: SetScriptTransaction => {
        let assetIssuerPublicKey = assetInfo (assetId) .extract (). issuerPublicKey
        sigVerify (tx.bodyBytes, tx.proofs [0], tx.senderPublicKey) &&
        sigVerify (tx.bodyBytes, tx.proofs [1], assetIssuerPublicKey)
    }

    # Team tokens can only be transferred between team members, other tokens can
    # translated if there is an account signature
    case t: TransferTransaction => {
        # If the transferred token is NOT a command token, then check the signature
        if t.assetId! = assetId then sigVerify (t.bodyBytes, t.proofs [0], t.senderPublicKey) else
        {
            # get the address of the recipient of tokens as a string
            let recipientAddress = addressFromRecipient (t.recipient) .toString ()

            # translate the address of the decentralized application of the command into the Address type
            let whiteListAddressValue = addressFromStringValue (whiteListAddress)

            # read the value from the repository of the command decentralized application by key
            # equal to the textual representation of the recipient address of the current transaction
            let addressIsAllowed = getBoolean (whiteListAddressValue, recipientAddress)

            # check that the value received from the storage is == true
            # otherwise (if false or Unit) then write false to the variable
            let addressInWhiteList = match addressIsAllowed {
                case b: Boolean => b == true
                case _ => false
            }

            # if the recipient's address is in the list of employees
            # or tokens are transferred to the command's decentralized application, then
            # check the transaction signature (must be signed with the account key)
            if ((addressInWhiteList || recipientAddress == whiteListAddress)) then {
                if (sigVerify (tx.bodyBytes, tx.proofs [0], tx.senderPublicKey))
                then true
                 else
                throw ("Signature is required")
            } else {
                throw ("You can send this token only to white-listed addresses")
            }
        }
    }

    # If a function of a decentralized application is called
    # then we only allow the command application to be called
    case i: InvokeScriptTransaction => {
        # get the address of the called application as a string
        let dappCalledAddress = addressFromRecipient (i.dApp) .toString ()

        # if the address of the called application is equal to the address of the command application, then
        # require user signature
        # otherwise we prohibit the call
        if (dappCalledAddress! = whiteListAddress) then throw ("You can call only dApp with address" + whiteListAddress + ", but you're trying to call" + dappCalledAddress) else
        if (sigVerify (tx.bodyBytes, tx.proofs [0], tx.senderPublicKey) == false) then throw ("Transaction should be signed with users key") else
        true
    }

    # We prohibit all other types of transactions, including exchange, token issuance, etc.
    case _ => throw ("Bad transaction type")
}

```

The scripts described above will satisfy the following requirements:

1. A unique token is issued for each team in Slack, which will be the team's internal currency
2. For each team member, a separate account is created, which stores the tokens of this team
3. Team tokens can only be transferred between team members, so the list of team members' addresses must be stored somewhere
4. Any team member can "burn" their tokens

However, two more requirements must be met:

1. Participation in the vote is carried out through calls to dApp functions with attached team tokens
2. For each vote, the user receives a unique NFT token, which confirms his vote

Any member of the team can initiate voting and fundraising for any purpose (hereinafter referred to as a fundraising campaign). To do this, you need to call the function of the decentralized application, specifying:

- unique identificator
- end time of fundraising / voting
- project implementation period
- the amount of fees at which the campaign is considered successful

In order not to disclose inside information, we will not provide a title or description in the decentralized application. Campaign data (title, description) can be stored in a centralized database available to company employees.

If the campaign was successful, that is, the amount collected is greater than the amount declared at the time of the campaign creation, then the tokens must be locked on the contract until the due date. After that, investors start voting on the implementation of the project. If the project, in the opinion of most investors, has been implemented, then the tokens on the contract are unlocked and the campaign creator receives them. Otherwise, the tokens remain on the contract forever. This scheme allows you to avoid situations when tokens are collected, and the stated goal is not realized.

Now let's implement the application step by step.

The function to start a fundraising and voting campaign can be as follows:

``` scala
@Callable (i)
func startFunding (id: Int, fundraisingEndTimestamp: Int, implmenetationEndTimestamp: Int, targetSum: Int) = {
    # current time
    let lastBlockTimestamp = lastBlock.timestamp

    # Campaign end time cannot be less than 60 seconds
    if (fundraisingEndTimestamp - lastBlockTimestamp - 60 <0) then throw ("End time should at least 60 seconds more than the last block time") else

    # The end time of fundraising cannot be less than the project release date
    if (implmenetationEndTimestamp <fundraisingEndTimestamp) then throw ("Implementation end time should more or equal to endTimestamp") else

    # Campaign ID must be unique
    if (isDefined (getInteger (this, keyFunding (id)))) then throw ("Funding with the same ID already exists") else

    # The minimum amount of tokens to collect is 1000
    if (targetSum <1000) then throw ("You cannot fundraise less than 1000 tokens")
    else {
        let fundingPrefix = "funding_" + id.toString ()
        [
            # save at what height the campaign was started
            IntegerEntry (fundingPrefix, height),
            # save the end time of the campaign
            IntegerEntry (fundingPrefix + "_timestamp", fundraisingEndTimestamp),
            # save the project execution date
            IntegerEntry (fundingPrefix + "_impl_timestamp", implmenetationEndTimestamp),
            # save the value of the goal (the number of collected tokens), at which the campaign is considered successful
            IntegerEntry (fundingPrefix + "_targetSum", targetSum),
            # create a key in which we will constantly store the actual number of tokens received by the target, and write 0 as the initial value
            IntegerEntry (fundingPrefix + "_raised", 0),
            # create a key in which we will store the number of votes for the project was implemented, and write 0 as the initial value
            IntegerEntry (fundingPrefix + "_release_votes", 0),
            # save the public key of the campaign creator (as a string for readability in the browser)
            StringEntry (fundingPrefix + "_owner", i.callerPublicKey.toBase58String (),
            # create a key in which we will store whether the creator of the target received his tokens after voting, and write false as the initial value
            BooleanEntry (keyReleasedTokens (id), false)
        ]
    }
}

```

We create many keys that we will use in other functions as well. Therefore, in order not to repeat ourselves and avoid typos, it makes sense to separate them into separate functions:

``` scala
func keyFunding (id: Int) = "funding_" + id.toString ()
func keyEndTimestamp (id: Int) = keyFunding (id) + "_timestamp"
func keyImplEndTimestamp (id: Int) = keyFunding (id) + "_impl_timestamp"
func keyTargetSum (id: Int) = keyFunding (id) + "_targetSum"
func keyOwner (id: Int) = keyFunding (id) + "_owner"
func keyRaised (id: Int) = keyFunding (id) + "_raised"
func keyReleaseVotes (id: Int) = keyFunding (id) + "_release_votes"
func keyReleasedTokens (id: Int) = keyFunding (id) + "_released"
```

All team members, except for the campaign creator, can give their tokens for project implementation or as votes. But the contribution of one user cannot exceed 33% of the required amount. To support a campaign, you need to call an application method, pass a unique campaign ID and attach tokens as a payment.

``` scala

@Callable (i)
func fund (id: Int) = {
    # get a riveted payment, if it doesn't exist, an exception will be thrown
    let pmt = i.payments [0] .extract ()

    # get campaign parameters
    let targetSum = getIntegerValue (this, keyTargetSum (id))
    let endTimestamp = getIntegerValue (this, keyEndTimestamp (id))
    let owner = getStringValue (this, keyOwner (id))

    # get the number of collected tokens at the current moment
    let raised = getIntegerValue (this, keyRaised (id))

    # get the number of tokens that the current user has already sent to this target
    # if absent, we get 0
    let alreadyFundedByUser = match getInteger (this, keyUserFunded (id, i.callerPublicKey.toBase58String ())) {
        case v: Int => v
        case _ => 0
    }

    # check that the attached tokens are indeed organization tokens
    if (pmt.assetId! = thanksTokenId) then throw ("You have to attach proper tokens with id:" + thanksTokenId.toBase58String ()) else

    # check that the campaign creator is not trying to fund the campaign himself
    if (owner == i.callerPublicKey.toBase58String ()) then throw ("You cannot fund your own target") else

    # check that the end of fundraising has not yet arrived
    if (endTimestamp> lastBlock.timestamp) then throw ("This target is finished already") else

    # check that the user's contribution to the goal is less than 33% after this transaction
    if (((alreadyFundedByUser + pmt.amount)> targetSum / 3 + 1)) then throw ("You cannot fund more than 33% of the whole sum which is" + ((targetSum / 3 + 1) - alreadyFundedByUser) .toString ())
    else {
        [
            # save the new number of tokens received from the current calling account for this campaign
            IntegerEntry (keyUserFunded (id, i.callerPublicKey.toBase58String ()), alreadyFundedByUser + pmt.amount),
            # update the amount of collected tokens at the current moment
            IntegerEntry (keyRaised (id), raised + pmt.amount)
        ]
    }
}
```

We have already implemented the functionality of creating a campaign and funding it, but if the fundraising was successful (the amount collected is greater than the amount specified at the time of creating the campaign), then the tokens should be locked for contracts until the due date.

After the due date, investors start voting on the fact of the project. If, in the opinion of most investors, the project was implemented, then the tokens are unlocked on the contract and the creator of the campaign receives them, otherwise the tokens remain on the contract forever.

Let's implement a function that campaign investors can call and confirm that the project has been completed. The absence of a vote will be considered a "Against" vote.

``` scala
@Callable (i)
func voteForRelease (id: Int) = {

    # get campaign parameters
    let endTimestamp = getIntegerValue (this, keyEndTimestamp (id))
    let implEndTimestamp = getIntegerValue (this, keyImplEndTimestamp (id))
    let votePower = getIntegerValue (this, keyUserFunded (id, i.callerPublicKey.toBase58String ()))
    let targetSum = getIntegerValue (this, keyTargetSum (id))
    let raised = getIntegerValue (this, keyRaised (id))
    let released = getBooleanValue (this, keyReleasedTokens (id))
    let owner = keyOwner (id)

    # get the number of tokens of investors who have already voted "For"
    let votedPower = getIntegerValue (this, keyReleaseVotes (id))


    # check that the campaign deadline has already passed
    if (implEndTimestamp> lastBlock.timestamp) then throw ("This target is not finished yet") else

    # check that the campaign was successful and raised the required amount
    if (raised <targetSum) then throw ("This target didn't reach target") else

    # check that voting on this campaign is not over yet
    if (released) then throw ("This target was released already") else

    # check that the current investor has not already voted for the campaign
    if (isDefined (getBoolean (this, keyUserVoted (id, i.callerPublicKey.toBase58String ())))) then throw ("You already voted for the target") else

    # if the number of those who have already voted "For" and the vote of the current investor in total
    # more than 50%, we transfer tokens to the creator of the campaign and record the fact of completion
    # otherwise we increase the number of tokens of investors who have already voted "For"
    if ((votedPower + votePower)> (raised / 2)) then {
        # campaign creator gets 80% of the collected amount
        # the remaining 20% ​​is held by the application as a commission
        let ownerPrize = raised - (raised / 1/5)
        [
            IntegerEntry (keyReleaseVotes (id), votedPower + votePower),
            BooleanEntry (keyReleasedTokens (id), true),
            ScriptTransfer (addressFromPublicKey (owner.toBytes ()), ownerPrize, thanksTokenId)
        ]
    } else {
        [
            IntegerEntry (keyReleaseVotes (id), votedPower + votePower)
        ]
    }
}
```

If the campaign was still not successful and did not collect the required number of tokens, investors should be able to withdraw their funds:

``` scala
@Callable (i)
func refundUser (id: Int) = {
    # get campaign parameters
    let endTimestamp = getIntegerValue (this, keyEndTimestamp (id))
    let targetSum = getIntegerValue (this, keyTargetSum (id))
    let raised = getIntegerValue (this, keyRaised (id))

    # get the number of tokens that were sent by the current investor
    let fundedAmount = getIntegerValue (this, keyUserFunded (id, i.callerPublicKey.toBase58String ()))

    # check that the campaign fundraising period has already ended
    if (endTimestamp> lastBlock.timestamp) then throw ("This target time is not finished yet") else

    # check that the campaign has NOT raised the required amount of funds
    if (raised> targetSum) then throw ("This target raised enough tokens") else

    # check that the current investor really supported the campaign
    if (fundedAmount == 0) then throw ("You have no power here") else
        [
            # reset the number of tokens from the current investor
            IntegerEntry (keyUserFunded (id, i.callerPublicKey.toBase58String ()), 0)
            # return tokens to the investor
            ScriptTransfer (i.caller, fundedAmount, thanksTokenId)
        ]
}

```

## User interface

We have implemented the main code in Ride, but the final application must also be able to interact with the user, messenger API, database, and so on. These questions are beyond the scope of this article. To understand how the application works for the end user, watch a video demo on the project website - [https://iambilly.app](https://iambilly.app). Better yet, install Billy for free on Slack and start using it.