# Chapter 3. Accounts and Keys

The first thing a person encounters when he starts using the blockchain is working with keys. Unlike classic web applications, where we have a username and password, blockchains only have keys that allow us to identify the user and the validity of his actions.
Each account has a public key and a corresponding private key. The public key is actually the account identifier (ID), while the private key allows you to form a signature. Waves uses signatures with [curve Curve25519-Ed25519](https://en.wikipedia.org/wiki/EdDSA#Ed25519) with X25519 keys (which is sometimes a problem, because not all libraries for languages ​​have support for X25519 keys programming).

The public and private keys are 32 byte values ​​that correspond to each other according to certain rules (you can find more details in the [EdDSA](https://blog.filippo.io/using-ed25519-keys-for-encryption/) description... It is important to understand several things that make Waves different from other blockchains:

- not any 32 bytes can be a private key
- the private key does not contain the public key (for example, in Ethereum, the private key contains the public one, therefore it has a size of 64 bytes, in Waves the public key is calculated each time for the private key)
- the signature using EdDSA is non-deterministic, that is, the same data can be signed with the same key and receive different signatures, since random values ​​are also used

## Key travels

Most users still come across keys not in the form of an array of bytes, but in the form of a seed phrase, often also called a mnemonic phrase. Any combination of bytes can be a seed, but Waves clients typically use 15 English words. Based on the seed phrase, the private key is calculated as follows:

1.the string is translated into a byte array
2.calculated hash `blake2b256` for the given array of bytes
3.calculate hash `keccak256` for the result of the previous step
4.the private key is calculated based on the previous step, an example of the function for this step is presented below

```, go
func GenerateSecretKey (hash [] byte) SecretKey {
    var sk SecretKey
    copy (sk [:], hash [: SecretKeySize])
    sk [0] & = 248
    sk [31] & = 127
    sk [31] | = 64
    return sk
}

```

In other words, or rather the code:
`privateKey = GenerateSecretKey (keccak256 (blake2b256 (accountSeedBytes)))`

Public and private keys are usually represented as `base58` strings like` 3kMEhU5z3v8bmer1ERFUUhW58Dtuhyo9hE5vrhjqAWYT`.

When sending transactions (for example, sending tokens), the user deals with the address, not the recipient's public key. The address is generated from the recipient's public key with some additional parameters: version of the address specification, network byte and check-sum. At the moment, there is only one version of the addresses on the Waves network, so the first byte in this sequence is `1`, the second byte is a unique network identifier that allows you to distinguish addresses in different networks (mainnet, testnet, stagenet, etc.). Network bytes for the above networks `W`,` T`, `S`, respectively. Thanks to the network byte, it is impossible to make a mistake and send tokens to an address that cannot exist on the network in which the transaction is sent (you cannot send tokens in the mainnet to an address in the testnet network). After the first two overhead bytes, there are 20 bytes obtained as a result of the blake2b256 and keccak256 hashing functions over the public key. This operation `keccak256 (blake2b256 (publicKey))` returns 32 bytes, but the last 12 bytes are discarded. The last 4 bytes in the address are the check sum, which counts as `keccak256 (blake2b256 (data))`, where `data` are the first 3 parameters (version, network byte and 20 bytes of the public key hash). The resulting sequence of bytes is converted to base58 representation to get something like this: `3PPbMwqLtwBGcJrTA5whqJfY95GqnNnFMDX`.

> Experienced developers at Waves use the features of address generation to determine which network the address belongs to by the type alone. Due to the fact that the first 2 bytes in the address are similar for all addresses in the same network, you can roughly understand which network the address belongs to. If the address looks like `3P ...`, then the address most likely belongs to the mainnet, and if the address starts with `3M ...` or `3N`, then you are most likely an address from testnet or stagenet.

## Working with keys

If, for some reason, the application needs to generate keys for the user, then you can use libraries for different programming languages. For example, in the JavaScript / TypeScript `waves-transactions` library, you can generate a seed phrase using the following code:

```js
import {seedUtils} from '@ waves / waves-transactions'

const seedPhrase = seedUtils.generateNewSeed (24);

console.log (seedPhrase);

// infant history cram push sight outer off light desert slow tape correct chuckle chat mechanic jacket camp guide need scale twelve else hard cement
```

The console will display a string of 24 words, which are the seed phrase of the new account. These words are a random subset of the dictionary found [in the @ waves / ts-lib-crypto library code](https://github.com/wavesplatform/ts-lib-crypto/blob/master/src/crypto/seed-words-list.ts) and which contains 2048 words.

In this example, I've generated 24 words, but by default, many Waves apps generate a set of 15 words. Why exactly 15 and does the safety increase if more words are generated?

15 words out of 2048 in any order is enough for the probability of generating two identical seed phrases to be negligible. At the same time, 24 words further reduce this probability, why not use larger meanings? The answer is simple - the more words we use, the more the user needs to write down and / or memorize and the more difficult it will be for him. The point of using a seed phrase (rather than a private key) is to simplify the user experience, and with 24 words, we noticeably degrade the user experience.

> What is the likelihood that someone will be able to pick up 15 words of a wallet? This question is asked by many users, so the user `deemru` on one of the forums about Waves even carried out [calculations] (https://wavestalk.ru/t/bezopasnost-koshelka-waves/123/2). I will give them below.

- We have 20 bytes of the public key hash in the address, which should match, these are 2 ^ 160 options.
- (Here we note that 15 words of 2048 (2 ^ 11) options each, which gives 2 ^ (11 * 15) = 2 ^ 165, that is, they overlap 160 bits with a margin of 5 bits, that is, 15 words are not taken from the ceiling, this is the minimum sufficient amount, more will be redundant, less will not cover all the bits of the public key in the address).
- Suppose we have a search speed of 2 million samples per second (for example, F72s_v2 (72 virtual CPUs, 144 GB memory) gives such results on Azure, it costs 171 thousand rubles a month).
- We start counting: 2 million is ~ 2 ^ 21 samples per second, for a year it will be 60 \ * 60 \ * 24 * 365 = 31536000 seconds, this is ~ 2 ^ 25, we get 2 ^ (21 + 25) = 2 ^ 46 samples in a year.
- The probability of finding a specific wallet is 1/2 ^ (160-46) = 1/2 ^ 114
- A million is ~ 2 ^ 20, then the probability of finding a wallet out of a million wallets is 1/2 ^ (114-20) = 1/2 ^ 94
- While it's hard ... let's suppose we have not 1 car, but a million: 1/2 ^ (94-20) = 1/2 ^ 74
- And not one year, but a million years: 1/2 ^ (74-20) = 1/2 ^ 54
- Multiply a million available capacities per million data centers: 1/2 ^ (54-20) = 1/2 ^ 34
- Well, this is a normal probability (which you can imagine at least in your head): 1 chance in 17 billion to find in a million years, in millions of data centers with a million machines in each 1 wallet in a million.
- Good luck.

Now let's get back to how to work with the seed phrase. Having a seed phrase, you can get a private key, public key and address. I'll show you again how to do this in JS, but do you remember that there are libraries for other languages ​​too?

```js

import {seedUtils} from '@ waves / waves-transactions';
import {
  address,
  privateKey,
  publicKey
} from '@ waves / ts-lib-crypto'


const seedPhrase = seedUtils.generateNewSeed (24);

console.log (privateKey (seedPhrase)); // 3kMEhU5z3v8bmer1ERFUUhW58Dtuhyo9hE5vrhjqAWYT
console.log (publicKey (seedPhrase)); // HBqhfdFASRQ5eBBpu2y6c6KKi1az6bMx8v1JxX4iW1Q8
console.log (address (seedPhrase, 'W')); // 3PPbMwqLtwBGcJrTA5whqJfY95GqnNnFMDX
```

Please note that in the `privateKey` and` publicKey` functions we pass only the seed phrase, while in the `address` we pass one more` chainId` parameter (aka the network byte). As you remember from the explanation above, the address itself contains such an additional parameter.

## How an account appears on the blockchain

We examined how the keys work, how the seed phrase, private and public keys are related, as well as how the address relates to them, but I did not mention one very important point that some novice developers forget about. Until the moment of performing any action with the account (sending a transaction from it or to it), the blockchain knows nothing about this account. If you generated an account (locally or in any client), but there were no transactions associated with this account (incoming or outgoing) in the blockchain, you will not be able to find any information about your account in the explorer or using the API. This is different from the behavior in centralized systems and APIs, so it might not be as intuitive or straightforward, but it's important to keep this in mind.