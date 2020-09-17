# 5 things I would like to know before I started developing decentralized apps

In conclusion of the chapter on decentralized applications, I would like to share my development experience and tell 5 things that I would like to know many years ago, when I just started making products using blockchain.

## 1. Blockchain - still as Spectrum

Many developers have heard about the problems of blockchain scalability, that blockchain is not about high load at all (by high I mean tens and hundreds of thousands of requests). But when I first got carried away with blockchain, I did not fully realize what awaited me. Sasha Ivanov once wrote in the article ["You can't calculate on the blockchain"](https://medium.com/wavesprotocol/you-cant-calculate-on-the-blockchain-906f9b110829) that the current level of the blockchain is Sinclair ZX Spectrum 8-bit computers.

These computers have played a very important role in the advancement of technology, but compared to modern personal computers and smartphones, they seem to be nothing more than a programmable calculator. In terms of the level of computing power for each individual application, the blockchain resembles the ZX Spectrum. But there is nothing to worry about: blockchain is not for computing speed, but for removing intermediaries.
The limitations of blockchain affect not only the complexity of programs (for example, there are very few decentralized applications - dApps - with hundreds of thousands of lines of code), but also how developers think. This picture illustrates well the problems of today's blockchain developers, very similar to those of game developers in 1996.

![Developers](../../assets/7-6-5-1-devs.png "Developers")

Common application developers may not think about many of the problems dApp developers face:

- The need to fight for every byte and for every computing operation
- The inability to easily update your application and deliver the update to customers (in this regard, the Waves blockchain compares favorably with competitors, as it provides such an opportunity)
- The high cost of a mistake.

Of course, there are advantages to all of these restrictions. For example, not being able to write whatever you want in code reduces security concerns. I talked about some other benefits at the [San Francisco Blockchain Week 2019] conference (https://www.youtube.com/watch?v=gMcif_ADWak).

## Different user experience

Back in 2014, when I started to be interested in the development of applications for the blockchain, I did not realize that along with the change in the principles of describing the business logic of applications, the user experience was changing.

For example, a user is accustomed to authorization in the application using a username and password, and to perform an operation with a dApp, a signature with a private key is required.

The private key is most often stored in a browser extension (see Waves Keeper), which becomes an additional barrier for PC users and a big headache on mobile devices.

Also, for each new user action - an additional step is added, which can completely "kill" the user experience and create big problems for the business.

Recently, products have appeared in which users are offered familiar algorithms, but at the same time decentralization and security are not sacrificed, for example, Waves Signer. But this segment is still at a very early stage of development.

In such conditions, it is extremely important to convey to the user why blockchain is needed in a particular application, what advantages it provides, and why it is worth putting up with a possible deterioration in user experience.

## Decentralization is not the default value

At the beginning of their journey, many dApp developers think that any product can be made better simply by adding blockchain and decentralization. In fact, this is not the case, because the blockchain, in addition to decentralization, leads to a change in user experience, and, most often, for the worse.

Understanding why you need blockchain will save you months and years of developing a product that will only get worse with blockchain. According to the famous investor and businessman Peter Thiel, in order to win customers and the market, your product must be 10 times better than competitors. And blockchain products are no exception.

Decentralization is very cool, but adding it to a process should increase its value in the eyes of the user at least several times. You don't say, "Our product is built on Postgres, so it is better than the competition." This also applies to blockchain.

There is no universal formula for determining the value of a blockchain in a specific solution. But it's worth starting with the question: "Which parties are involved in the process that I am trying to improve, and why do they not trust each other?" If only one party is involved, or there is no trust issue, chances are good that you don't need blockchain.

## Power in composition (composability)

Typical for many startups and companies is the [NIH](https://en.wikipedia.org/wiki/Not_invented_here#:~:text=Not%20invented%20here%20(NIH)%20is,and%20costs%2C%20such%20as%20royalties.)  [Not invented here](https://en.wikipedia.org/wiki/Not_invented_here#:~:text=Not%20invented%20here%20(NIH)%20is,and%20costs%2C%20such%20as%20royalties.) syndrome. The desire to "do it all" in the blockchain space can lead to very undesirable consequences.

An important value of the blockchain is that it is a unified interface for communication between different decentralized applications. If you are familiar with the concept of ["data bus"](https://en.wikipedia.org/wiki/Enterprise_service_bus), then you can draw a parallel with the blockchain. Imagine having access to data from a large number of other applications right in your code runtime.

Another analogy that will help you understand what composition is - Lego bricks, from which you can assemble something unique.

For example, the Waves protocol uses the LPoS consensus algorithm, which allows you to receive income from leasing (staking). The Neutrino Protocol from the Ventuary Lab team uses this algorithm in their stablecoin to enable the user to stake their tokens. Any other application can accept Neutrino stablecoins as a means of payment. This is composition: some applications build on others, using and complementing them.

Many Web 2.0 services have APIs that also allow you to integrate with them. But there are several important differences:

- In Web 2.0 applications, users are often required to register and obtain special API keys that give access to data and can be revoked by providers at any time. Access keys are not required to receive data or call methods of decentralized applications, since all applications run in a common space
- For classic Web 2.0 applications, there are a huge number of different API protocols (authorization, data encoding, etc.). This problem does not exist for dApps. You just need to know what arguments what functions take. Even if the application has no documentation, you can see it yourself on the blockchain.

## It's only the beginning

Bitcoin is more than 10 years old, and it may seem that all applications have already been created, all fresh ideas have been implemented and the era of interesting startups in the blockchain world is over. This is far from the case. Every few years new trends, opportunities, technologies appear in the blockchain. We can say that every few years, or even more often, the market offers new players a chance. The most interesting projects are ahead of us!

I talked about the fact that the blockchain is developing extremely quickly in my [2019 Results](https://medium.com/@ikardanov/2019-in-the-blockchain-world-through-the-eyes-of-developer-advocate-e329aee5aa1), which I recommend you read if you haven't already. New products like Gravity open up opportunities for a lot of innovation. For example, with the advent of Gravity, it will be possible to create decentralized applications that work in several networks at once. Whoever will be the first to take advantage of these opportunities and in one application will combine, for example, the best aspects of Bitcoin, Waves, Ethereum, will definitely be able to do something innovative.