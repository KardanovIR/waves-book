# UTX processing features

Waiting list transactions are never fun, they want to get into a block! How do you determine which transaction should get into the block first? It would be possible to make a simple queue and be guided by the principle "Who got up first, that and the slippers", but this approach is not optimal for miners in the network. It is much more profitable for them to put transactions with a higher commission in the block. But here, too, everything is not so simple, you remember that there are different types of transactions in Waves. Each type has its own minimum commission, set in consensus, so processing depending on the size of the commission will also not lead to the expected result. For example, sending `InvokeScript` transactions with a minimum commission of 0.005 Waves will always hit the block before a Transfer transaction with a 0.001 Waves commission. What to do?

The first thing you can think of is sorting transactions based on cost per transaction byte. A node spends resources on validating a signature for a transaction, and the larger the transaction is in size, the more resources will be spent on it. For example, a 140 kb data transaction will be validated several tens of times longer than a Transfer transaction less than a kilobyte in size. Let's talk with examples. Let's say we have 2 transactions:

- Data transaction of 100 kb and a commission of 0.01 Waves
- Transfer transaction in the size of 1kb and with a commission of 0.001 Waves

Which transaction will be the first in the queue? The one that was received first, because in terms of 1 byte of a transaction, the commissions for these 2 transactions are equal.

The diagram below shows how exactly the node processes transactions before entering the block, as well as while it is there. In the diagram, you can see the `cleanup` function, which is constantly executed in the background and checks if the transactions in UTX have become invalid (expired, the sender's balance has become zero due to another transaction and cannot pay the commission, etc.). and whether there is a need to remove them from there.

![UTX pool details](../../assets/37F7DE3C-90E8-49FD-BD5D-16779F29D3F0.png "UTX pool details")