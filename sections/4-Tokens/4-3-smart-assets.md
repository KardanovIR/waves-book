# Smart assets

Tokens on Waves are not smart contracts by default (unlike Ethereum), so they are freely tradable, and token holders can do whatever they want with them. But if you really want to change the behavior of the token and add any restrictions, then this can be done by turning a regular asset into a smart asset. We have already considered smart accounts, whose behavior differs from ordinary accounts in that before sending a transaction from such an account, its script is executed and it must return `true`. Smart assets are very similar to smart accounts. In the same way, a script on Ride is added to the asset, which is executed for each operation with this token and must return `true` for the transaction to be considered valid. But there are several distinctive features of a smart asset.

## Adding a script to an asset

The main difference between smart assets and smart accounts is that if a token is issued without a script, then it cannot be added to it later. This is done so that the creators of tokens do not have the ability to deceive users, for example, by sending them tokens, the circulation rules of which may change. It is also important to clarify that if a token is issued with a script and this script does not directly prohibit, then such a script can be updated. You can say that the owners of tokens can also cheat in this way, but in this case, users will at least see from the very beginning that the token is a smart asset and can mean it.

Another reason for the impossibility of adding a script to assets released without this is that the functionality of smart assets appeared in the third year of the life of the Waves blockchain, and to give simple tokens, which have been living on the network for several years, the ability to change the rules of the game on the go without considering opinions users, it would not be entirely correct.

But what if we want to issue a token, but have not yet written its script? It is enough to put `true` in the form of a script (or rather a compiled version of such a script in base64 format - `AwZd0cYf`) as a script. Such a script will not prohibit any operations with the token, but will allow you to update the script in the future and set the rules you need.

In Sections 5 (Transactions) and 6 (Ride), we will take a closer look at the specifics of setting a script for assets and the distinctive features of Ride for tokens.

## Smart asset example

An example of a smart asset used in the Waves ecosystem is the Waves Reward Token, which was released by the Waves team and distributed to many platform users so that they can later transfer them to ambassadors (and only them) as a thank you for their help. The Waves team buys these tokens in the future. Thus, the most active ambassadors earn tokens from users and sell them to the Waves team. The WRT token code ensures that it can only be transferred to ambassadors whose list is administered by the Waves team and stored in one of their accounts.