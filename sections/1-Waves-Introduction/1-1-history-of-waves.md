# 1.1 History of Waves

## What is Waves?

Waves is a Proof-of-Stake permissionless, general-purpose blockchain platform created since 2016 to help mainstream blockchain technology. Waves blockchain platform is one of the most mature (both in terms of age and in view of the large number of projects) and easy for beginner developers who want to take advantage of blockchain to the maximum benefit. Within the framework of this book, we will analyze the main technical features, talk about the advantages and consider a lot of real code, but before we do this, let's talk a little about the history of Waves in order to better understand the origins of certain features.

## Start project

Waves as a blockchain began in 2016, when the founder of the platform [Alexander Ivanov](https://twitter.com/sasha35625) initiated a fundraising event as part of an ICO. From the very beginning, the platform has positioned itself as a general-purpose blockchain, without a focus on certain specific areas of application. The main task that the platform was going to solve (and in many ways successfully solved it) is throughput. At the beginning of 2016, there were very few blockchains that could process hundreds of transactions per second. In fact, only Bitcoin (and its forks like Litecoin) with 7 transactions per second and Ethereum with 15 transactions / sec fully worked on the market. So the problem of blockchain bandwidth was extremely urgent.

### Major milestones in the development of the project

The successful fundraising for the ICO was only the beginning of the project, then it was necessary to implement everything promised as well as possible. Initially, the technological base of the [Scorex](https://github.com/ScorexFoundation/Scorex) framework was chosen . Almost nothing is left of the framework itself in the project's code base, but Scorex is written in Scala, which determined the technological stack of the protocol development for a long time. Until recently, the implementation of a node (that is, a protocol) in Scala was the only one, only relatively recently there was also an implementation in Go. It is worth noting that at the time of this writing, the Go version was lagging behind the Scala version in terms of capabilities, we will talk about this in the following sections. The Waves mainnet (hereinafter referred to as mainnet) was launched in December 2016. The project had features from the very beginning: easy release of its tokens / assets (by sending one transaction) and PoS with a leasing (staking) model. We will analyze these features in further sections.

Other notable milestones in the history of the protocol include the following dates:

- Issue of DEX (hybrid exchange) in 2017. A logical continuation of the native support for the issuance of tokens using a transaction (not a contract as in Ethereum) was the release of the matcher, which ensured the operation of the decentralized exchange.
- In the same year, the Waves NG protocol was implemented, which allowed us to achieve good throughput. Waves NG was implemented based on the ideas outlined in [article](https://www.usenix.org/system/files/conference/nsdi16/nsdi16-paper-eyal.pdf). These proposals were aimed at improving the Bitcoin protocol, but were never implemented there, but were implemented in Waves.
- In the summer of 2018, the Ride smart contract language was released, which allowed changing account logics, and in January 2019, it became possible to write contracts for tokens
- In the summer of 2019, Ride reached the mainnet for full-fledged decentralized applications (Ride4dApps)
- In the fall of 2019, the first of its kind (among the main blockchain projects) monetary policy appeared

### Impact of Waves history on protocol

The history of the creation of Waves strongly influenced the technical details of the project. For example, the Scorex framework is rooted in the Nxt project, another Proof-of-Stake blockchain with native tokens. Both features were inherited by Waves from Nxt. The ease of creating tokens and the speed of the protocol later made the project the second most popular platform for issuing tokens and launching ICOs (right after Ethereum). A large number of projects on Waves have used the blockchain specifically for working with tokens. Since 2019, projects related to financial services have been actively developing, so we can say that the focus of Web3 projects on the platform is now the task of creating an open ecosystem of financial products.