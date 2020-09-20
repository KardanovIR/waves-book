# Chapter 8. Developer's best friends

As my girlfriend says, developers have no friends, but we know that they are not, because a developer's best friends are development tools.

During the development of Web3 applications, it is necessary to constantly interact with the nodes of the blockchain, both to receive information and to send data as a transaction. On the other hand, building Web3 applications implies user interaction, including requests for transaction signatures to take actions or check the status of accounts, etc.

There are several resources and tools in the Waves ecosystem that can be useful for these purposes, let's take a look at them.

## Node REST API

As you may recall from Chapter 2 about configuring the node (in fact, you probably forgot about these 100+ pages), the Waves node has a built-in REST API for receiving and sending data. Many other blockchains use JSON RPC rather than REST, but it is the presence of REST that makes communication with the node as easy as possible. By default, the REST API nodes are disabled in the configuration, but there are public nodes that allow making requests to receive data and send transactions:

- `nodes.wavesplatform.com` for mainnet
- `nodes-testnet.wavesplatform.com` for testnet
- `nodes-stagenet.wavesplatform.com` for stagenet

The REST API node is divided into 2 large parts - the public part, which is accessible without a key, and the private part, which is available only when the API Key is passed. API Key of public Waves nodes cannot be obtained (in the vast majority of cases this is not required), but if you still need methods that require a key, then you need to deploy your node.

> Note that the API Key hash is specified in the config file (how to get the hash correctly is described [in the documentation](https://docs.wavesprotocol.org/en/waves-node/node-configuration#how-to-generate-hash-of-the-api-key)), and when calling methods, the API Key itself is passed in cleartext.

Many libraries for Waves in different programming languages ​​(we will talk about them below) support methods for communicating with the API of the node. All of them support the `broadcast` method, which sends the signed transaction to the network via the REST API. But some libraries have other useful functions, for example, in `waves-transactions` there are functions for requesting the state of the storage of any account as a whole or for a specific key (`accountData` and `accountDataByKey`), a function for requesting balance or information about the script of a specific account by its address (`balance`, `assetBalance` and `scriptInfo`), functions of waiting for some blockchain height and waiting for the transaction to enter the block (`waitForHeight`, `waitForTx`, `waitForTxWithNConfirmations`), etc.

The REST API of a node is quite limited in terms of filters and searching for transactions by fields, but for such operations it is better to use data services.

## Data service API

Data services are available at `https://api.wavesplatform.com/ v0/docs/` for use in mainnet or `https://api-test.wavesplatform.com/v0/docs/` for testnet, but also you can use it on your servers by deploying from the project repository on Github - `https://github.com/wavesplatform/data-service`.

Data services constantly download information about transactions and blocks on the blockchain and store it in a relational database (Postgresql) to provide greater search flexibility (thanks to indexing). If you still want to use data services on your servers, keep in mind that the Waves blockchain relational database is over 250 gigabytes.

Date services can also save information on trading on the DEX, for example, information about the history of transactions in various pairs, exchange candles, etc.

## Libraries for programming languages

Within the framework of this book, all examples were using JavaScript and the `waves-transactions` and `waves-crypto` libraries, but besides them there are libraries for other programming languages:

- JavaScript / TypeScript - [waves-transactions](https://github.com/wavesplatform/waves-transactions)
- Python - [PyWaves](https://github.com/PyWaves/PyWaves/)
- PHP - [WavesKit](https://github.com/deemru/WavesKit)
- C # - [WavesCS](https://github.com/wavesplatform/WavesCS)
- Go - [GoWaves](https://github.com/wavesplatform/gowaves)
- Java - [WavesJ](https://github.com/wavesplatform/WavesJ)
- Kotlin - [WavesSDK](https://github.com/wavesplatform/WavesSDK-android)
- Swift - [WavesSDK](https://github.com/wavesplatform/WavesSDK-iOS)

The libraries support the latest protocol updates and have many similar primitives. Only the `PyWaves` library is relatively different.

In addition to those listed above, there are libraries for C, Rust and Elixir, but they do not support signing all types of transactions (or their latest versions) and are actively looking for maintainers.

## Node extensions - gRPC and Matcher

The Scala version of the Waves node (remember that there is also a Go version) allows you to expand your functionality. Extensions have the ability to subscribe to various events in the blockchain and implement any functionality based on this. For example, [Matcher](https://github.com/wavesplatform/dex), which is the heart of the decentralized exchange, uses the extension functionality to communicate with the node and get the current state of balances and blockchain. Another example of an extension is [gRPC server](https://docs.wavesprotocol.org/en/waves-node/extensions/grpc-server/), which allows you to communicate with a node not via text REST, but via binary [gRPC](https://en.wikipedia.org/wiki/GRPC), which in some cases can be many times faster.

Another example of an extension for a node is [Waves Node Tools Extension](https://github.com/msmolyakov/waves-node-tools-extension) by Maxim Smolyakov, which automates the payment of remuneration to the node's leasers. If you have a leasing pool and people are leasing tokens to you with the expectation of receiving a share from the earned node, then you can use this extension. The extension records lease receipts, the moments of block mining by a given node and the contribution of each leaser to the generating balance at the time of block creation. In the future, the script distributes the earned tokens to the leasers, keeping the node owner's commission. The extension can even notify the owner of the node about events such as the generated block or the moment of payments by calling a webhook.

Installing an extension for a node usually consists of 2 steps:

- installing a `.deb` package or downloading a `.jar` file with extension logic
- updating the node configuration with the name of the extension package

In the configuration file of the node, extension parameters can also be set. For example, installing the Waves Node Tools Extension means installing the `.deb` package and adding the following lines to the configuration file:

``` hocon
# add an extension to the list of node extensions
waves.extensions + = "im.mak.nodetools.NodeToolsExtension"

# Waves Node Tools Extension settings

node-tools {
  webhook {
    url = "SPECIFY YOUR ENDPOINT" # example: "https://example.com/webhook/1234567890"
    method = "POST"
    headers = [] # example: ["Content-Type: application / json; charset = utf-8", "Authorization: Basic dXNlcjpwYXNzd29yZA =="]
    body = "% s" # example for Integram: "" "{" text ":"% s "}" ""
  }
}
```