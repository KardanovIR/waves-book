# Regular accounts vs. smart accounts

As you probably already understood from the previous section, Waves uses the account model, and not the inputs and outputs (input and output) as in Bitcoin. But the Waves model differs not only from Bitcoin, but, for example, from Ethereum, which also has accounts. The main difference is that Waves has several account types. Let's break them down in order.

## Regular accounts

The work of regular accounts is as simple and intuitive as possible. Each account (with a certain public key) "possesses" certain tokens and stores the data in its own storage. To make an action from an account, you need to form a transaction, sign it with the private key of this account and send it to the network. ** A valid transaction signature allows you to perform operations from this account. ** When validating a transaction, nodes actually verify that the signature and the body of the transaction match. They also need to check some parameters from the blockchain, for example, if an account wants to send tokens, does it have them on its balance. The easiest way to understand how it works is by analogy - an account is a barn with which you can do anything (take away <s> cheese </s> tokens from there, for example) if you have a key to the barn lock.

But a regular account can be turned into a smart account that behaves differently.

## Smart accounts

If you put a script on a regular account that sets other validation rules for **outgoing** transactions, then it will become a ** smart account. ** The smart account will also "own" tokens, but in order to do something with them ( translate, burn, exchange, etc.), you must not provide a signature, but satisfy the conditions described in the body of the script. The model is very similar to that in Bitcoin, with one exception - Waves does not use a primitive Bitcoin script, but the much more powerful Ride language. This book will have a separate section devoted to Ride, so now let's talk more about the concept of smart accounts. The code on the Ride is sent to the network using a script install transaction (`SetScript`) and turns a regular account into a smart one.

Smart accounts are a type of smart contracts. In general, there are 3 types of smart contracts that occur in nature:

- Simple smart contracts for managing accounts (multisignature, escrow, etc.)
- Complex contracts with non-trivial logic (Crypto-Kitties, Bancor, etc.)
- Token contracts (ERC-20, ERC-721)

Smart accounts are representatives of the first category, being intended for basic account operations. Waves has tools for creating complex contracts, which will be discussed in Section 6, and Waves does not require contracts at all to create tokens, which will also be discussed in Chapter 4.

Smart accounts **only allow outgoing transactions** (not incoming) to be validated. The smart contract script is a predicate that is executed when an attempt is made to send a transaction from an account, and the transaction is considered valid only if the body of the script returns `true`. The body of the script can contain various logic based on:

- Transaction parameters (for example, the size of the commission, the recipient of the transfer transaction, the type of transaction, etc.)
- Data from the blockchain (number of the last block in the blockchain, data from the storage **of any** account)
- Transaction signatures

Continuing the analogy with the barn, we can say that a smart account is a barn with a different type of lock, which is not opened by a key (or not only by a key), but can also rely on time, the contents of the barn (or any other barns), the identity of the opening etc. I think many barns in many collective farms would be safer if there were locks that can only be opened with 5 keys from different people.

## When to use smart accounts

Smart accounts are very lightweight, do not require a lot of computing power from the nodes, while at the same time they cover a large number of cases. The most common uses for smart accounts are:

- **Multi-signature.** For example, there is an account with tokens from 3 parties, and you can spend them only if 2 parties out of 3 agree.
- **Escrow.** Often, when performing transactions in real life between 2 parties, it is necessary to have some kind of arbitrator who fixes the fact of an action on one side and the need to transfer money to it. Selling an apartment is a great example where money must be transferred when handing over the keys. This can be done using an escrow contract.
- **Atomic exchange.** Exchange of tokens in two networks, when tokens are blocked by parties in 2 different networks using a secret. As soon as one side unlocks the tokens on network A with a secret, the other will see this secret and can pick up tokens on network B.