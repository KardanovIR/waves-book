# 5 things I would like to know about before I started developing decentralized applications



## Blockchains are still like Spectrum

Many of the developers have heard about the problems of blockchain scaling, about the fact that this is not about highload even once, but when I just started to get involved in blockchain, I did not fully realize what awaited me. Sasha Ivanov once wrote in his article ["You can't calculate on the blockchain"](https://medium.com/wavesprotocol/you-cant-calculate-on-the-blockchain-906f9b110829) that the blockchain is now it's like the Sinclair ZX Spectrum 8-bit computers in the past. These computers played a very important role in the evolution of technology, but compared to modern personal computers and telephones, they were like a programmable calculator. Blockchain is now very similar to the ZX Spectrum in terms of computing power for each individual application. And this is correct, because the blockchain is not for the speed of calculations, but for the removal of intermediaries.

All blockchain restrictions affect not only the complexity of programs (for example, very few decentralized applications with hundreds of thousands of lines of code), but also **how we think as developers**. The picture below explains the difference a lot, because decentralized app developers were like game developers in 1996.

![img](https://media.discordapp.net/attachments/694073260509233212/714476772615520336/d391f9ea-2091-8043-a54c-a844862373a0.png)

Developers of regular applications may not think about the problems that are familiar to developers of decentralized applications:

- The need to fight for every byte and every computing operation
- The lack of the ability to easily update your application and deliver to customers (in this regard, the Waves blockchain compares favorably with competitors and provides such opportunities)
- High cost of error

Of course, there are advantages to all of these restrictions. For example, not being able to write whatever you want reduces the field of security concerns. I talked about some other advantages during the San Francisco Blockchain Week 2019 conference - https://www.youtube.com/watch?v=gMcif_ADWak

## Different user experience

When I first became interested in the development of blockchain applications (back in 2014), I did not fully realize that along with the change in the principles of describing the business logic of applications, the user experience also changes. For example, a user is accustomed to being authorized with a username and password in the application, but in the case of a blockchain, he needs to use his private key for signing. Most often, it is customary to store the private key in a browser extension (see Waves Keeper), which is an additional barrier for PC users and a big headache for mobile users. It also adds an extra step for each transaction that is created, which can completely destroy the user experience and complicate your business many times over.

Recently, products have begun to appear that try to offer familiar templates to users without sacrificing decentralization and security, for example, Waves Signer (https://github.com/wavesplatform/signer). However, we are only at the beginning of this path and there are still many stages of development ahead.

In such conditions, it becomes extremely important to inform the user why blockchain is needed in your application, what advantages it provides and why it is worth putting up with a possible deterioration of the user experience. And this is the next thing that I would like to know at the beginning of my journey.

## Decentralization is not a default value

Many dApps developers at the beginning of their journey think that any product can be made better simply by adding blockchain and decentralization there. In fact, this is not the case, because the blockchain, in addition to decentralization, also leads to a change in user experience, most often for the worse. Understanding why you need blockchain can save you months and years of developing a product that has only gotten worse with blockchain. According to the famous investor and businessman Peter Thiel, your product must be 10 times better than the competition in order to win customers and the market. And blockchain products are no exception. Decentralization is very cool, but adding such a characteristic to some process should increase the value in the user's eyes at least several times. You don’t say something like "Our product is made on Postgres DBMS, so it is better than competitors", or with blockchain.

There is no universal formula for determining whether the blockchain adds value, but the question to start with is "Which parties are involved in the process that I am trying to improve and why do they not trust each other?" If there is only one side or there is no trust issue, then with a high degree of probability you still need to think about whether you need a blockchain.

## Power in composition (composability)

It is normal for many startups and companies to have **NIH** (Not invented here) syndrome (https://en.wikipedia.org/wiki/Not_invented_here). The desire to "do it all yourself" in the blockchain sphere can lead to completely undesirable consequences. An important value of the blockchain lies in the fact that it is a unified interface for communication between different decentralized applications. If you are familiar with the concept of "data bus" (https://en.wikipedia.org/wiki/Enterprise_service_bus), then the blockchain is very close to this. Imagine that you have access to data from a large number of other applications right in your code runtime.

Another analogy that will help you understand what composition is - lego bricks, which you can easily use to assemble something unique.

For example, the Waves protocol uses the LPoS consensus algorithm, which allows you to receive income from leasing (staking). The Neutrino app from the Ventruay Lab team uses this in their stablecoin to give the user the option to stake their tokens. Anyone else can accept Neutrino stablecoins as a means of payment. This is composition, where some applications build on, use, and complement others.

In the ordinary Web2 world, many services have their own APIs that also allow you to integrate with them, but there are several important differences:

- Web2 applications often require registration, obtaining special API keys that give access to data and can be revoked by providers at any time. In the case of blockchain applications, you do not need access keys to receive data or call application methods, since all applications run in a common space
- In the world of classic Web2 applications, there is a huge number of different API protocols (authorization, data encoding, etc). In the world of blockchain applications, there are simply no such problems. You only need to know which arguments which functions take. And you can see it yourself in the blockchain, even if the application has no documentation.

## It's only the beginning

Bitokin is already over 10 years old and it may seem that all applications have already been made, all "fresh" ideas have already been implemented and the era of interesting startups in the blockchain world is over. This is far from the case, because every few years new opportunities, technologies, and trends appear in the blockchain world. We can say that the market provides new players with a chance every few years or even more often.

It is safe to say that the most interesting projects are ahead of us.