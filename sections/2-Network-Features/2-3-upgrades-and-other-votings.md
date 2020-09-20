# Protocol updates and other polls

As you could understand from the previous section, the blockchain protocol, especially Waves NG, is a completely non-trivial thing. But like any protocol, it can and should change over time to get better. But everything is not so simple here. The Waves development team cannot just release updates and order everyone to upgrade, or say that those who do not upgrade will stop working - this is against the principles of decentralization. Many blockchains follow the path of hard forks, just a new functionality is released with a new version of the node, then whoever needs it installs it and starts mining with the support of the new feature. Whoever agrees switches to a new chain, who does not continue to mine the old one. This path is far from the best and can lead to countless forks, so adding new functionality or changing the consensus rules in Waves is possible only through the procedure for proposing new functionality and voting.

The procedure for changing the parameters of consensus through voting is often called governance. We will not use this word, because governance in Waves is now limited to two types of voting, while in many other blockchains there are much more possible changes (account bans, hard changes in account balances and other atrocities that do not get along well with the principles of decentralization).

## Procedure for proposing new functionality

There is an unwritten rule in the Waves ecosystem (there can be no written rule, because decentralization) that all new functions and consensus changes must go through a discussion procedure. All proposals for changes are so-called Waves Enhancement Proposal or WEP. Each WEP has a sequential number, a well-defined structure and questions that the proposal must answer. The WEP form was suggested on the Waves forum in a dedicated section, but at the moment the main discussions and suggestions are on github.

So, each proposal is formulated in the form of a WEP, then this proposal is discussed by all interested parties, corrections are made, the wording is clarified, etc. In the end, anyone can implement the proposed WEP in the code of a node (the repository is open) and send a Pull Request to add changes to the main development branch. The Waves team is responsible for the quality of the code in the repository, so if there are no quality problems, the code will be added to the main branch and will be included in the build of the next release, which are also published on github. But this does not mean that the new code will start working, because each new functionality (hereinafter a feature, from the word "feature") must be approved not only by the developers, but also by the community, for this a voting procedure is launched.

Here is a link to [Waves WEP](https://forum.wavesplatform.com/c/waves-improvment-proposals/23)

## Voting for Consensus Rules

As soon as the owner of the node installs a new version, he will have the opportunity to vote for the new feature to be activated. Each feature has an order of the number by which the voting and identification goes, usually the numbers of new features are listed in the release descriptions on Github, but you can also look at the API of the node. A node owner can add a supported feature number to his configuration in the `waves.features.supported` array. After that (or rather, after restarting the node), the number of the supported feature is added to each generated block from this node. That is, in the binary representation of the block (in which the work is in progress), a new value appears with the feature number.

In order for a feature to be activated, it is necessary that its support is at least 80% - at least 80% of the blocks during the voting period must contain information about the support of the feature. The voting periods begin at each multiple of ten thousand blocs (block number 100,000, 110,000, 120,000, etc.). Simply put, at least 80% of blocks with numbers 100000-109999 contain information about support for a new feature.

In fact, the governance at Waves is very close to how the US presidential election system works. Residents of the country do not directly vote for the president, they elect representatives from each state (the number of representatives varies from state to state. Representatives already directly vote for the president of the United States.

In Waves, token holders can directly vote on feature activation, however, in most cases they do not have their own nodes and lease their tokens, thus transferring their voting rights to the owner of the leasing pool. It is important to understand that they can cancel the lease at any time if the pool owner does not vote the way they would like, which will reduce the number of blocks that this pool will generate. The largest pools in Waves often vote among leasers using decentralized apps (we'll look at an example of such voting in section 7), so representative *democracy* / decentralization can be transparent.

If the feature has been supported by more than 80% of the steak, then it will be activated 10,000 blocks after the end of the voting period. For example, if the voting was during blocks 10000-19999, then the feature will become active at an altitude of 30,000. This delay allows you to check once again that everything is normal and the new feature will not cause forks.

<! - TODO: why is this delay actually needed? ->

If you look at the code of the node or in the API (`/ activation / status`), you will notice that each feature has the following possible statuses:

- **VOTING** - voting is in progress
- **APPROVED** - the feature has been approved, but not yet activated (due to a delay of 10,000 blocks)
- **ACTIVATED** - the feature is activated, all nodes on this chain must support this feature

As you can see, the feature does not have the `REJECTED` status, that is, voting for a feature can go on indefinitely.

## Block Bounty Voting

There is another type of node voting in Waves that is not found almost anywhere - block reward voting. In 2016, the Waves blockchain was launched with a limited emission of 100 million tokens, which had already been created at the time of the network launch. But in the fall of 2019, the community realized that, after all, the emission model is more lively, so there was a proposal for an update - WEP-7, which went through the voting procedure and the feature was activated on block N. Now, for each block generated, the miner receives not only commissions (and that is 40% of his block and 60% of the previous one, do you remember about Waves NG?), But also receives Waves "generated from the air". Approximately every minute, the total number of Waves in nature increases by a certain value. Which value is the subject of the node vote.

Every 100 thousand blocks (approximately 2 and a half months), a vote begins for the amount of the reward. At the moment of feature activation, N was set to 6 Waves. Every 100,000 blocks, this value can change by no more than 0.5 Waves, and then provided that more than 50% of miners are supported.

Voting for block rewards is done in a slightly different way from features. Node owners can set the reward value that they would like to see in the long term in their configuration file in the `waves.reward.desired` parameter. At the end of the voting period, it is calculated how many blocks contain the desired reward more than the current one, and if more than 50%, then the reward is increased by 0.5 Waves for the next 110,000 blocks (the beginning of a new voting period + the voting period itself).

Some community members asked why `waves.reward.desired` simply did not include` + `or` -`, since the reward will not be changed by more than 0.5 Waves anyway. Specifying the desired reward in the long run eliminates the need for frequent configuration changes. You can set the value to `10` and not go into the configuration every voting period, since you will vote for an increase until the reward reaches 10 Waves per block. And as soon as it reaches (if it does), the node will stop voting for increasing the reward. So simple.

Here is a link to [WEP 7 - An alternative proposal for Block Generation Reward](https://forum.wavesplatform.com/t/wep-7-an-alternative-proposal-for-block-generation-reward/17013) 
<! - TODO: Add a link to what height WEP-7  was the activation ->