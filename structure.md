# Approximate book structure

The structure of the book is in the mode of constant edits, the primary source of the structure is the directories in [sections](./sections).

## 1. Introduction to the Waves blockchain

The section tells about the history of the protocol, describes the main features of the protocol and how it differs from other blockchains. The basic principles of work in the section are not affected, since it is implied that the reader is either already familiar with them, or can familiarize himself with the course on Сoursera. Section structure:

### [1.1 History of Waves](./sections/1-Waves-Introduction/1-1-history-of-waves.md)

### [1.2 Approaches to Waves Protocol Development](./sections/1-Waves-Introduction-2-development-principles.md)

### [1.3 Distinctive features of the Waves blockchain](./sections/1-Waves-Introduction/1-3-features-and-USPs.md)

_

## 2. Important features of the Waves network protocol

This section focuses on how Waves works at the level of the network protocol and node interaction. This section focuses on how the Waves networking protocol can impact the dApp development experience on the platform.

### [2.1 The Waves node and how it works, its configuration](./sections/2-Network-Features/2-1-node-configuration.md)

### [2.2 Mining Process (Waves NG)](./sections/2-Network-Features/2-2-mining-and-waves-ng.md)

### [2.3 Protocol updates and other polls](./sections/2-Network-Features/2-3-upgrades-and-other-votings.md)

_

## 3. Waves Accounts

This section explains how to create Waves accounts in and out of a node. This section does NOT cover cryptography related issues.

### [3.1 Keys in the Waves blockchain](./sections/3-Accounts/3-1-keys.md)

What is a seed phrase and how is it related to a key, why is a seed phrase of exactly this length, is it possible to pick it up. The private key, public key, and address, as they are related. How an account appears on the blockchain.

### [3.2 Regular accounts vs. smart accounts](./sections/3-Accounts/3-2-accounts-vs-smart-accounts.md)

_

## 4. Tokens in Waves

This section explains what tokens are first-class citizens and what you can do with them. They also talk about the motivation for such decisions and how it will help us in the future.

### 4.1 Releasing, Releasing, Burning, and Changing Asset Information

### 4.2 Sponsoring Transactions

Potential problems when sponsoring transactions. The maximum transaction cost in Waves and for some reason someone paid that much.

### 4.3 Smart Assets

### 4.4 Asset and DEX trading
_

## 5. Transactions in Waves

Analysis of all types of transactions, including deprecated. Explains transaction versioning, serialization, and signing process. An overview of how UTX works - how transactions enter the block, in what order, what microforks are, and why a valid transaction may not enter the blockchain.

### 5.1 Parsing all types of transactions

### 5.2 Signing transactions

### 5.3 Features of UTX Operation
_

## 6. Ride

Description of the Ride language, its main features, syntax and analysis of small examples (ride-introduction). Overview of innovations in Ridev4.

### 6.1 Language syntax

### 6.2 Language restrictions

### 6.3 Runtime features

### 6.4 Tooling

Surfboard, IDE, VS Code, Docker images, Tests Framework
_

## 7. Examples of implementation of decentralized applications

Analysis of step-by-step examples with explanation of requirements and gradual implementation in the form of contracts.

### 7.1 Oraculus

### 7.2 Billy

### 7.3 HotPotatoToken

### 7.4 Ride best practices
_

## 8. Other important aspects for development

### 8.1 API nodes and data services

### 8.2 Waves Keeper and Waves Signer

### 8.3 Libraries for different programming languages

### 8.4 Node extensions - gRPC and Matcher

_

## 9. Platform development priorities

### 9.1 Gravity hub

### 9.2 Interchain communication

_

## 10. Conclusion