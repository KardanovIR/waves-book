# Standard library

The Ride standard library includes a relatively small set of functions, many of which are related to cryptography (hash functions, signature verification functions, etc.). Let's take a look at the most commonly used and what features these functions have.

The first versions of Ride were designed for such simple smart accounts as multisignature, freezing of funds and work with escrow, so the standard library was minimal. Let's take a look at a basic example of a smart contract - multisignature.

## Multi-signature 2 of 3

The main idea of ​​multisignature 2 of 3 is that on one account we accumulate funds that are managed by 3 other accounts. The decision to transfer funds from an account with funds can be made subject to the consent of at least 2 manager accounts. Consent in terms of blockchain and cryptography means the presence of a signature from the given public keys. Let's say Alice, Bob and Cooper manage a multi-signature account and only if at least 2 of them are signed, funds from the account can be transferred.

The first thing we need to do is to define in advance 3 public keys that can sign transactions from an account with funds. Public keys are byte arrays that can be represented in Ride by three types of primitives `base16 ''`, `base58''` and `base64''`. The default representation for keys is `base58''`.

``` scala
{- # STDLIB_VERSION 4 # -}
{- # CONTENT_TYPE EXPRESSION # -}
{- # SCRIPT_TYPE ACCOUNT # -}

@Verifier (tx)
func verify () = {
    #define public keys
    let alicePubKey = base58'5AzfA9UfpWVYiwFwvdr77k6LWupSTGLb14b24oVdEpMM '
    let bobPubKey = base58'2KwU4vzdgPmKyf7q354H9kSyX9NZjNiq4qbnH2wi2VDF '
    let cooperPubKey = base58'GbrUeGaBfmyFJjSQb9Z8uTCej5GzjXfRDVGJGrmgt5cD '
    true
}
```

Note that the function will return the value `true`, which will allow all transactions from this account, without any signature checks. But don't be alarmed, this is an intermediate state of our script, and `true` (or `false`) as a return value is necessary so that the online IDE does not show us a script compilation error. Let's continue with the implementation of our multisignature script.

The Ride standard library has a `sigVerify` function that verifies the signature of a message. It takes 3 arguments:

- message
- signature
- public key

And it returns a boolean value (`true` if the signature matches the public key and message,`false` otherwise).

The public keys in our example have already been declared, it remains to find 2 more arguments - the message and transaction signatures. The object of the current outgoing transaction `tx` has a field `bodyBytes` that contains the message to be signed. Also, the `tx` object has a `proofs` field that contains the transaction signatures in a list. The simplest verification of the message signature looks like this:

``` scala
    sigVerify (tx.bodyBytes, tx.proofs [0], base58'5AzfA9UfpWVYiwFwvdr77k6LWupSTGLb14b24oVdEpMM ')
```

Signature verification for Alice, Bob and Cooper might look like this:

``` scala
    (sigVerify (tx.bodyBytes, tx.proofs [0], alicePubKey) && sigVerify (tx.bodyBytes, tx.proofs [1], bobPubKey)) # alice & bob
    ||
    (sigVerify (tx.bodyBytes, tx.proofs [0], alicePubKey) && sigVerify (tx.bodyBytes, tx.proofs [2], cooperPubKey)) # alice & cooper
    ||
    (sigVerify (tx.bodyBytes, tx.proofs [1], bobPubKey) && sigVerify (tx.bodyBytes, tx.proofs [2], cooperPubKey)) # bob & cooper
```

However, a more elegant way is to verify the signature and write to the variable 1 or 0, depending on the result. By adding these 3 variables and making sure that the sum is greater than 2, we can be sure that at least 2 signatures were correct.

``` scala
{- # STDLIB_VERSION 4 # -}
{- # CONTENT_TYPE EXPRESSION # -}
{- # SCRIPT_TYPE ACCOUNT # -}

@Verifier (tx)
func verify () = {
    #define public keys
    let alicePubKey = base58'5AzfA9UfpWVYiwFwvdr77k6LWupSTGLb14b24oVdEpMM '
    let bobPubKey = base58'2KwU4vzdgPmKyf7q354H9kSyX9NZjNiq4qbnH2wi2VDF '
    let cooperPubKey = base58'GbrUeGaBfmyFJjSQb9Z8uTCej5GzjXfRDVGJGrmgt5cD '

    #check whoever provided the valid proof
    let aliceSigned = if (sigVerify (tx.bodyBytes, tx.proofs [0], alicePubKey)) then 1 else 0
    let bobSigned = if (sigVerify (tx.bodyBytes, tx.proofs [1], bobPubKey)) then 1 else 0
    let cooperSigned = if (sigVerify (tx.bodyBytes, tx.proofs [2], cooperPubKey)) then 1 else 0

    #sum up every valid proof to get at least 2
    aliceSigned + bobSigned + cooperSigned> = 2

}
```

The proofs array is always 8, however, values ​​may not be present anywhere in this array. That is, when sending a transaction, signatures can lie both in the first 3 fields of the array, and in the last three, or under indices 1, 4 and 5. In the next section, we will consider how to use `FOLD <N>` so as not to imply the presence of keys in some fields, but check all the options.

> Important: the list of `proofs` in transaction fields always has a dimension of 8, regardless of how many elements it actually contains.

In addition to the `sigVerify` signature verification function, there is a `RSA` verification function - `rsaVerify`. `sigVerify`, which works with the signature for the Curve25519 curve (ED25519 with X25519 keys, hereinafter for simplicity we will call" Waves signature ") is not deterministic, that is, there can be an unlimited number of signatures for the same key-message pair. The RSA signature is deterministic, in other words, there can be only one valid RSA signature for the same message and private key.

## zk-SNARKs

There is one more data verification function in Ride - `groth16Verify`. This function is intended for verification [zero-expansion proofs] (https://en.wikipedia.org/wiki/Non-interactive_zero-knowledge_proof).

> The purpose of zero knowledge proofs is for the verifier to verify that the verifier possesses knowledge of a secret parameter called evidence that satisfies some relationship without disclosing the evidence to the verifier or anyone else.

The `groth16Verify` function is similar to the crypto primitives used in the Zcash blockchain. An example of using `groth16Verify` for a prototype decentralized application that anonymizes a sender of funds can be found in the [Anonymous transactions prototype](https://github.com/wavesplatform/anonymous-transactions-prototype) repository.

## Working with primitives

There are 4 main primitive data types in Ride:

- numbers (only integer)
- boolean values ​​(boolean)
- strings
- arrays of bytes (byte vector)

For the last 2 types (string and byte vector) there are functions that make it easier to work with them. For example, the function `drop (data: ByteVector | String, n: Integer)` can remove the first N bytes or characters from a byte array or string. The `take (data: ByteVector | String, n: Integer)` function can, on the contrary, leave only the first N bytes. If you need to take bytes or characters not from the beginning, but from the end, then there are the functions `dropRight` and `takeRight`. And for even more convenience there is a `size ()` function.

Working with strings in Ride has become more convenient with the release of the standard library version 3, which introduced the following features:

- `contains (substring: String)` - check if the substring is included in the string
- `indexOf (substring: String)` - returns the index of the first occurrence of a substring
- `lastIndexOf (substring: String)` - returns the index of the last occurrence of a substring
- `split (delimiter: String)` - splits the string by delimiter and returns an array of strings
- `size ()` - returns the number of characters in the string

## Type conversions

A common need for programming in strongly typed languages ​​is type conversions. To do this, Ride has a huge number of functions that allow you to do `toBytes`, `toInt`, `toString`, `toUtf8String` (for an arbitrary byte array). There are also more specific functions like `toBase16String`, `toBase58String`, `toBase64String`, which convert an array of bytes to a string in one of the representations - `HEX`, `base58` or `base64`.

One of the most commonly used types in Ride code is `Address`, and there is a function `addressFromString` for convenience of working with it.

## Getting data from the blockchain

In Ride scripts, it is quite often necessary to work not only with the parameters of the called function or the transaction being sent, but also with data from the blockchain, which can be divided into 2 categories:

1. Data about entities in the blockchain (account balances, information about tokens or blocks)
2. Data from the account storage

The most commonly used functions from the first category are `assetInfo (assetId: ByteVector)` and `blockInfoByHeight (N: Integer)`. The first returns information about the token with the specified `assetId`. This includes parameters such as the public key of the asset creator or the title and description. The second function returns information about the block with the specified number. To get information about the current (last) block, you can use the global variable `lastBlock`, which also contains a structure with information about the block. The most useful block parameter can definitely be called `timestamp`, which contains the block generation time and is the best field to orient to the time in the code of your decentralized application (not the transaction timestamp).

The second category of functions allows you to read data from the storage of any account (not only the current one on which the script is running). As you might have guessed, there are 4 functions for reading different data types - `getInteger`, `getBinary`, `getBoolean`, `getString`. Each function takes an address and a key as an argument - `address: Address, key: String`. To read from the account on which the script is executed, it is enough to pass `this` as the first argument. All functions return `Union (T | Unit)` since the specified key may not exist. If you are sure that the function will exist, then you can use the functions `getIntegerValue`, `getBinaryValue`, `getBooleanValue` and `getStringValue`. The difference between the latter is that in the absence of a key, they will return an exception and the function will terminate.

If you do use `getInteger`, `getBinary`, `getBoolean` or `getString`, then Ride has functions that allow you to extract data from `Union` or return a default value or an error:

- `value ()` and `extract ()` - get data from a variable or return an error without a description
- `valueOrElse (a: T | Unit, b: T)` - return data from a variable, if there is not `Unit`, otherwise return the second argument
- `valueOrErrorMessage (a: T | Unit, b: String)` - return data from a variable, if there is not `Unit`, otherwise throw an error with the message `b`

The `isDefined ()` function may also be useful, which checks that its argument is not `Unit` (remember that `Unit` can be considered an analogue of `null`).

## Working with lists

Ride lists can be declared with square brackets `[]` and contain any data type. Some functions in the standard library (for example, `split` for strings) return lists as the result of execution. For the convenience of working with them, the standard library has functions:

- `containsElement (list: List [T], element: T): Boolean` - checks if there is an element in the list
- `indexOf (list: List [T], element: T): Int | Unit` - returns the first index of the element or `Unit` if the element is not found
- `lastIndexOf (list: List [T], element: T): Int | Unit` - - returns the last index of the element or `Unit` if the element is not found
- `min` - returns the minimum value in the list
- `max (List [Int]): Int` - returns the maximum value in the list
- `median (List [Int]): Int` - returns the median value in the list
- `makeString (List [String], separator: String): String` - return a string with all elements of the delimited list
- `cons (T, List [T]): List [T]` - adds a new element to the beginning of the array
- `size (List [T]): Int` - returns the number of elements in the array
- `getElement (List [T], Int): T` - returns the element of the array at the given index

In addition to the functions of the standard library, Ride also has operators for working with lists:

- `: +` - operator for adding an item to the end of the list
- `++` - operator for concatenating lists

## Other features

In this book, we will not consider all the available functions, but I consider it necessary to mention 2 more categories of functions. Much of the blockchain is related to hashing functions, and Ride also has such functions - `keccak256`,`blake2b256` and `sha256`.

The second category of functions that are very often useful are the mathematical functions `pow` for exponentiation,`log` for calculating the logarithm and `fraction (value: Int, numerator: Int, denominator: Int)`, which multiplies the first 2 arguments and divides to the third, avoiding integer variable overflow.

## Function families

Remember, every feature in Ride has a `complexity` that determines its complexity. For some functions in Ride there are analogues that have less `complexity` due to the fact that they have a limit on the size of the argument. For example, the `sigVerify` function has a complexity of 200 and the maximum size of the first argument is 150 kb, but the standard library contains the functions `sigVerify_16kb`, `sigVerify_32kb`, `sigVerify_32kb`, `sigVerify_128kb`, with the values `complexity` 100, 110, 125 and 150, respectively. If you are sure that your argument cannot be larger than the values ​​specified in the function name, then you can use them and your script will have less complexity.

Not only is sigVeriry a family of functions, but also hashing functions, rsaVerify and groth16Verify, for example. You can always find a complete list of function families in the documentation.