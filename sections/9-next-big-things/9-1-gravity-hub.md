# Gravity

At the end of 2019 in Berlin, Alexander Ivanov, the founder of the Waves protocol, announced the creation of Gravity, a new product of the team, which is designed to solve several problems at once.

We are now seeing a rapid development in the development of a variety of blockchain protocols, platforms and experimental developments, almost the same rapid growth of the Internet we saw 20 years ago. The diversity of technologies generates progress, at the same time it leads to a weakening of the adoption of technology by the masses (mass adoption). Product developers and organizations are now forced to choose a specific technology or protocol on which to build their applications. In turn, users and audiences of other protocols lose access to such applications. All this makes the ecosystem as a whole very fragmented, fragmented and economically ineffective.

Each decentralized system with an internal token economy is closed in terms of the exchange of information both with other networks and with the outside world. To transfer data from the outside world to the blockchain network, as well as communication between different blockchain networks, it is necessary to trust the so-called oracles. The paradox of the need to trust individual elements in systems designed to remove elements of trust arises. The search and development of solutions that create conditions for increased reliability and security of the use of blockchain networks with oracles, as well as communication between networks, is a key task in the industry at the moment.

Finally, an effective solution to the problem of cross-blockchain communication and data oracles opens up opportunities for horizontal scaling of products that are built on blockchain protocols through the mechanism of so-called sidechains.

To date, there are already projects that solve the problems described above: Chainlink, CosmosHub, Polkadot and others. However, most of the existing solutions have three common disadvantages:

- Focused on developing their own, complementary blockchain, instead of really being a blockchain agnostic solution.
- Introduce a new level of complexity into the system in the form of a token with special economic properties and a floating, highly volatile exchange rate.
- In reality, they are not a complex blockchain-agnostic system of oracles that supports communication of blockchain networks with the outside world, cross-blockchain communication and funds transfers, as well as sidechains within one coherent structure.

[Gravity](https://gravity.tech/) is designed to solve the indicated problems and is a solution to the following problems, sorted in order of priority:

- Blockchain agnostic decentralized cross-blockchain communication: Decentralized applications across different blockchains must be able to exchange data or values. For example, a Waves account holder should be able to work with the Uniswap app on the Ethereum network, without having to create an Ethereum account and buy Eth tokens. Or any Ethereum account holder should be able to use the Neutrino dApp and stake without having to create a Waves account and buy Waves on exchanges. All this will be automatically provided by the Gravity system.
- Economic motivation of agents to provide data from the real world and other blockchains: Gravity provides data providers with the opportunity to monetize their services, expressed in native tokens of Gravity-supported blockchain platforms
- Having the ability to receive data from the real world and from other blockchains without the need to trust a centralized entity opens up opportunities for scaling networks using sidechains
- Providing developers with a technical variety so that they can choose the most suitable tools: virtual machines or non-Turing-complete programming languages; fixed or variable fees; consensus algorithms (Proof of Work, Proof of Stake, Leased Proof Of Stake, BFT, etc.).

> Simply put, Gravity solves 2 problems:
> 1.problem of oracles - through their decentralization (we looked at problems in chapter 7 on the example of Oraculus)
> 2. the problem of incompatibility of blockchain protocols - through the ability to transfer data and funds from one blockchain to another

**Unlike other similar platforms, Gravity does not use its own token, which compares favorably with competitors.**

The scheme of work of Gravity is shown in the diagram below:

![Gravity](../../assets/9-1-0.png "Gravity")

Development of Gravity is currently in active phase, research work is underway and discussions on the best solution architecture are underway, so if you are interested in participating, you can do it in [Github Gravity](https://github.com/orgs/Gravity-Tech) ...

The full technical description and principles on which Gravity is built can be found in the [White Paper](https://gravity.tech/docs/whitepaper.pdf).