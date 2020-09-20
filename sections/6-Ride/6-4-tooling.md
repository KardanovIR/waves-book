# Tools for developing decentralized applications

For the convenience of developing decentralized applications on Waves, there are a large number of different tools. It is worth starting, first of all, with the blockchain explorer, which is located at [wavesexplorer.com](https://wavesexplorer.com) and allows you to analyze data in blocks, all transactions and UTX both in the main network and in stagenet and testnet.

However, if we are talking about the development of decentralized applications, and not just use, then several basic questions arise:

1. In what environment should I write code for smart accounts, smart assets and decentralized applications?
2. How to test the written code? What are the options for automatic and manual testing?
3. How to debug the code?
4. How to deploy?

Let's take a look at what tools are available for this.

## Development environment

The easiest way to get started writing code, testing and working with accounts is to use the online IDE, which is available at [https://waves-ide.com](https://waves-ide.com/). It has Ride syntax highlighting, smart hints, type inference, a compiler, a console for working with the `waves-transactions` library, and even a REPL (read-eval-print loop) for Ride, which allows Ride expressions to be executed right in the browser. There are also examples of code on Ride, examples of integration tests in JavaScript, the ability to manage accounts and send transactions using the web interface. The online IDE is great for testing contracts in stagenet and testnet. Waves tokens for these networks can be obtained for free using the faucet in wavesexplorer at the addresses `https: // wavesexplorer.com / stagenet / faucet` and` https: // wavesexplorer.com / testnet / faucet`, but no more than 10 Waves every 10 minutes.

However, for a more professional contract development, I recommend using other tools.

The [Ride for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=wavesplatform.waves-ride) extension is the first essential tool for professional development using the Waves blockchain. Installing this extension allows you to get syntax highlighting and hints for files with the `.ride` extension.

![Visual Studio Code Extention for Ride](../../assets/6-4-0-vs-code-ext-in-store.png "Visual Studio Code Extention for Ride")

In addition to syntax highlighting, the extension adds an interactive console to VS Code (just like in an online IDE), which allows you to run functions from `waves-transactions`,` waves-crypto` and several other specialized ones.

![Visual Studio Code Extention for Ride](../../assets/6-4-1-vc-code-ext.png "Visual Studio Code Extention for Ride")

## Local environment

During development, you can interact with nodes from stagenet or testnet, which are available at the addresses `http: // nodes-stagenet.wavesnodes.com /` and `https: // nodes-testnet.wavesnodes.com /`, but the most convenient option is the use of a private blockchain from one single node.
You can run such a blockchain if you have Docker installed. The launch is carried out with a simple command:

``` bash
docker run -d -p 6869: 6869 wavesplatform / waves-private-node
```

After launching the command, the local blockchain will be launched as a Docker container, the node's API will be available at `http: // localhost: 6869 /`, and all 100 million tokens will be on the account balance with the seed phrase `waves private node seed with waves tokens `.

![Local node with API](../../assets/6-4-2-local-node.png "Local node with API")

You can find detailed information about the Docker image with this node in [this repository](https://github.com/wavesplatform/private-node-docker-image).

The advantages of this approach are:

- Reduced block time: in testnet and stagenet, blocks are generated once a minute, while in your private network there will be every 10 seconds. This saves time when running integration tests.
- Control over all tokens and no need to request tokens using a faucet
- Full control over the node and API performance. The Waves Protocol team tries to ensure maximum availability of public nodes, but this is not always possible and in some cases APIs for stagenet or testnet may not be available.
- API responses from public nodes are cached, which can cause unexpected errors.

After starting the node, you can also launch a local blockchain explorer that will work with your node. This is done in the same way by deploying a Docker image:

``` bash
docker run -d -e API_NODE_URL = http: // localhost: 6869 -e NODE_LIST = http: // localhost: 6869 -p 3000: 80 wavesplatform / explorer
```

Please note that when expanding, the API address of our node with a private blockchain is indicated.

After deploying the image, the browser will be available at the address `http: // localhost: 3000`:

![Local explorer](../../assets/6-4-3-local-explorer.png "Local explorer")

## Testing the code

At the time of this writing, it is possible to write only integration tests for decentralized applications on Ride. There are no tools for Unit tests yet. Integration testing in the case of Ride means that the written code is compiled, deployed using `SetScript`,` SetAssetScript` or `Issue` transactions on an asset or account, and transactions are executed that check the correctness of the script behavior. In other words, there is direct work with the blockchain (not emulation!) And real transactions are sent.

Integration tests can be written in Java using the [Paddle](https://github.com/msmolyakov/paddle) library or in [JavaScript] using the online IDE or the `surfboard` library.

Surfboard can be installed from npm (provided you have node.js and npm) with the following command:

``` bash
npm install -g @ waves / surfboard
```

After that, you will have access to the `surfboard` utility right in the console. The `surfboard init` command will initialize a new project, which will contain a configuration file and directories for tests (`. / Test`) and scripts for Ride (`. / Ride`). The configuration file allows you to configure settings for working with different types of networks, account parameters, etc.

![Surfboard](../../assets/6-4-4-surfboard.png "Surfboard")

In the `. / Test` directory, you can create any files with the` .js` extension and write integration tests in them using the `Mocha` test framework. In addition to the `Mocha` itself, functions from` waves-transactions` and several additional functions and variables are available in the test file:

- `setupAccounts ({[key: string]: number})` - allows you to create new accounts at the beginning of the script and transfer tokens to them from the master seed
- `compile (file: File): String` - allows you to compile the contents of the file
- `file (path: String): File` - allows you to get the contents of a file
- `accounts` - an object that stores the seeds of accounts created by the` setupAccounts` function

Descriptions of these and other functions are available [in the documentation](https://wavesplatform.github.io/js-test-env/globals.html). You can find test examples in the online IDE or in the [ride-examples](https://github.com/wavesplatform/ride-examples) repository.

Running tests in the directory can be done using the command `surfboard test`, but if you want to run a specific file, and not all files in the directory`. / Test`, then you can execute `surfboard test my-scenario.js`.

## Debugging Ride Scripts

For debugging Ride scripts, it is customary to use 2 main techniques.

The online IDE and Surfboard have a REPL that allows you to enter the code and immediately get the result of the execution. The REPL allows not only performing basic operations, but also declaring variables, working with a real blockchain (for example, reading the state of accounts), calling functions of the standard library

![Surfboard REPL](../../assets/6-4-5-surfboard-repl.png "Surfboard REPL")

In more complex situations, when you already have a full-fledged script that needs to be debugged, the `throw ()` function from the Ride standard library comes to the rescue, which allows you to throw an error and a text description to it. In the error text, you can also return the values ​​of variables, however, at the moment of exception, the node will return the contents of the stack, the values ​​of variables in the function, etc.

## Manual Application Testing

If you prefer manual testing or want to play around with applications already deployed on the web, you can use the [https://waves-dapp.com](https://waves-dapp.com/) website. You can simply specify the account address of the desired decentralized application and Waves-Dapp will show all available functions, what parameters they accept and allow you to call any of them. The tool can be useful for testing your application when you do not have an interface or tests for some functions, or you need to change the settings of your application.

![Waves Dapp](../../assets/6-4-6-waves-dapp.png "Waves dapp")