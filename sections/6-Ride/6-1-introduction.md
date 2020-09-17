# Chapter 6. The Ride Programming Language

Ride is a compiled, statically typed, lazy execution functional programming language for building decentralized applications. The language was created with the goal of helping developers write error-free code.

Ride is not a Turing complete language. It has no loops (in the classical sense), recursions, and provides many restrictions that make applications easy to understand and debug. At the same time, using Ride and all the capabilities of the Waves blockchain, you can build Turing-complete systems.

## History of creation

The main ideological inspirer of the creation of Ride is Ilya Smagin. The language was originally aimed at implementing simple cases like multi-signature and first appeared on the Mainnet in the summer of 2018, allowing you to write scripts to create smart accounts. In January 2019, it became possible to write scripts for smart assets on Ride, and in July 2019 it became possible to write full-fledged decentralized applications thanks to a release called Ride4dapps.

The philosophy of the language and its creators comes down to a few simple rules:

1. A programming language is a tool for implementing specific cases. The complication of the language and the addition of new constructions are done only if there are cases and businesses that lack this.
2. Turing completeness together with scalability are hardly achievable within the framework of a public blockchain system, so the language should be convenient without this characteristic.
3. Ride is a specific language for writing decentralized applications, not a general purpose language, so the language has specific constructs.

The language has been heavily influenced by Scala and F #. This is not to say that Ride is very similar to any of these languages, but people who know these languages ​​will find it easier to start writing in Ride. At the same time, the language is simple for developers in all other languages, the initial acquaintance with the language and tools usually takes about an hour. By the end of this chapter, you will have learned almost the entire language and all the basic constructs.

Although Ride is simple, it provides many options for the developer. Let's move on to the syntax of the language.

## Hello world

``` scala
func say () = {
  "Hello world!"
}
```

Functions are declared using the `func` keyword. Functions return types, but they do not need to be declared, since the compiler will infer them. In the example above, the `say` function will return the string` "Hello World!" There is no `return` keyword in the language because Ride is expression-based (everything is an expression) and the last expression is the result of a function.

## Blockchain

Ride is intended to be used inside the blockchain and there is no way to access the file system.

Ride functions can read data from the blockchain and return transactions as a result, which will be written to the blockchain.

## Comments

``` scala
# This is a comment

# Multi-line comments cannot be created in the language

"Hello world!" # You can write comments in such places too
```

## Directives

Every Ride script must start with a compiler directive. There are 3 possible types of directives with different possible meanings.

``` scala
{- # STDLIB_VERSION 4 # -}
{- # CONTENT_TYPE DAPP # -}
{- # SCRIPT_TYPE ACCOUNT # -}
```

`STDLIB_VERSION` specifies the version of the standard library. The latest version in mainnet is 3, in stagenet it is 4.

CONTENT_TYPE specifies the type of file you are working on. Currently there are types `DAPP` and` EXPRESSION`. The `DAPP` type allows you to declare functions, terminate the execution of the script with some transactions (changes in the blockchain) and use annotations, while` EXPRESSION` ends with a boolean expression (`true` or` false`).

SCRIPT_TYPE specifies the type of entity to which we want to add the script and change the default behavior. Ride scripts can be attached to `ACCOUNT` or` ASSET`.

``` scala
{- # STDLIB_VERSION 4 # -}
{- # CONTENT_TYPE DAPP # -}
{- # SCRIPT_TYPE ASSET # -} # dApp type cannot be used for ASSET
```

Not all combinations of directives are correct. The example above will not work, because the `DAPP` content type is only valid for accounts, while the EXPRESSION type is available for tokens (assets) and accounts.

## Functions

``` scala
func greet (name: String) = {
  "Hello," + name
}

func add (a: Int, b: Int) = {
  a + b
}
```

The type must follow the name of the argument.

As with many other languages, functions cannot be overloaded. This helps keep the code readable and easy to modify.

Functions can only be used after they have been declared.

## Variables

``` scala
let a = "Bob"
let b = 1
```

Variables are declared and initialized using the `let` keyword. This is the only way to declare variables in Ride. All variables in Ride are immutable. This means that you cannot change the value of the variable after the declaration.

The type of the variable is determined, or more precisely, it is inferred (type inference) based on the value on the right side.

Ride allows you to define variables within any function or in the global scope.

``` scala
a = "Alice"
```

The above code will not compile because the variable `a` is undefined. All variables in Ride must be declared using the `let` keyword.

``` scala
func lazyIsGood () = {
  let a = "Bob"
  true
}
```

The above function will compile and return `true` as a result, but the` a` variable will not be initialized because Ride is lazy, which means that all unused variables are not evaluated.

``` scala
func callable () = {
  42
}

func caller () = {
  let a = callable ()
  true
}
```

The `callable` function will also not be called, since the` a` variable is not used.

Unlike most languages, variable reuse is not allowed. Declaring a variable with a name that is already in use in the parent scope will result in a compilation error.

## Basic types

The main base types are shown below:

``` scala
Boolean # true
String # "Hey"
Int # 1610
ByteVector # base58 '...', base64 '...', base16 '...', fromBase58String ("..."), etc.
```

### Strings

``` scala
let name = "Bob"
name + "is cool!" # lines will be concatenated because the + sign is used

name.indexOf ("o") # 1
```

In Ride, a string is a read-only byte array. String data is encoded using UTF-8.

Only double quotes can be used to denote strings. Strings are immutable like all other types. This means that the function of finding a substring in a string is very efficient: no copying is done, no additional selections are required.

All operators in Ride must have values ​​of the same type on both sides. The code below will not compile because `age` is of type Int and` "Bob is" `is a string:

``` scala
let age = 21
"Bob is" + age # won't compile
```

To make the code work, we have to convert `age` to` String`:

``` scala
let age = 21
"Alice is" + age.toString () # this is how it works!
```

## Special types

``` scala
List # [16, 10, "hello"]
Nothing #
Unit # unit
```

Ride has several types that "** look ** like ~~ ducks ~~ in Scala, swim like ~~ ducks ~~ in Scala, and quack like ~~ ducks ~~ in Scala". For example, the types `Nothing` and` Unit`.

Ride does not have a null type like many other languages. Usually, built-in functions return type `Unit` instead of` null`.

``` scala
"String" .indexOf ("substring") == unit # true
```

### Lists

``` scala
let list = [16, 10, 1997, "birthday"] # collection can contain different data types
let second = list [1] # 10 - the second value from the list

```

To work correctly with lists in Ride, they must always have a known size, because there are no loops or recursions.

List has no fields, but there are functions in the standard library that make them easier to work with.

``` scala
let list = [16, 10, 1997, "birthday"]

let last = list.getElement (list.size () - 1) # "birthday", postfix function call size ()

let lastAgain = getElement (collection, size (collection) - 1) # same as above
```

The `.size ()` function returns the length of the list. Note that this value is read-only and cannot be changed.

``` scala
let initList = [16, 10] # init value
let newList = cons (1997, initList) # creates a new list with initLinit elements and a new value - [1997, 16, 10]
let newestList = initList ++ newList # merges 2 lists into one
```

You can add a new element to an existing list using the `cons` function. The original list will not be changed, `cons` will return a new list. You can concatenate two lists using the `++` operator.

The Ride standard library has functions that make it easier to work with lists, for example:

- `containsElement (list: List [T], element: T): Boolean` - checks if there is an element in the list
- `indexOf (list: List [T], element: T): Int | Unit` - returns the first index of the element or` Unit` if the element is not found
- `lastIndexOf (list: List [T], element: T): Int | Unit` - - returns the last index of the element or` Unit` if the element is not found
- `min` - returns the minimum value in the list
- `max (List [Int]): Int` - returns the maximum value in the list
- `median (List [Int]): Int` - returns the median value in the list
- `makeString (List [String], separator: String): String` - return a string with all elements of the delimited list

### Union types

``` scala
let valueFromBlockchain = getString ("3PHHD7dsVqBFnZfUuDPLwbayJiQudQJ9Ngf", "someKey") # Union (String | Unit)
```

Union types are a very convenient way to work with abstractions, `Union (String | Unit)` shows that the value is the intersection of these types.

If Ride had custom types, the following example could be parsed:

``` scala
type Human: {firstName: String, lastName: String, age: Int}
type Cat: {name: String, age: Int}
```

`Unioin (Human | Cat)` is an object with one field `age`. Usually, `Union` is returned as a result of function calls when, depending on the parameters, the language runtime could receive different types.

``` scala
Human | Cat => {age: Int}
```

We can use pattern matching to figure out the real type:

``` scala
let t = ... # Union (Cat | Human)
let age = t.age # OK
let name = t.name # Compiler error
let name = match t {# OK
  case h: Human => h.firstName
  case c: Cat => c.name
}
```

For example, the `getString` function for reading strings from the account store returns` Union (String | Unit) `because some keys (and their values, respectively) may not exist.

``` scala
let valueFromBlockchain = getString ("3PHHD7dsVqBFnZfUuDPLwbayJiQudQJ9Ngf", "someKey")
let realStringValue = valueFromBlockchain.extract ()

# or
let realStringValue2 = getStringValue (this, "someKey")
```

To get the real type and value from `Union`, you can use not only pattern matching, but also the` extract` function, which will interrupt the script if the value is `Unit`. Another option is to use specialized functions such as `getStringValue`,` getIntegerValue`, etc., whose behavior will be identical (an exception will be thrown if the value is not in the storage or a different data type is stored for the specified key).

``` scala

{- # STDLIB_VERSION 3 # -}
{- # CONTENT_TYPE EXPRESSION # -}
{- # SCRIPT_TYPE ACCOUNT # -}

match (tx) {
    # example of a script with a ban on updating key values
    # read-only storage
    case t: DataTransaction => {
        if (getInteger (this, t.data [0] .key) .isDefined ()) then throw ("Key is already used")
        else true
    }
    case _ => false
  }

```

## If

``` scala
let amount = 1610
if (amount> 42) then "I claim that amount is bigger than 42"
  else if (amount> 100500) then "Too big!"
  else "I claim something else"
```

The `if` statements are quite simple and similar to most other languages, except for two differences:` if` can be used as an expression (the result is assigned to a variable) and the `else` clause is always required.

``` scala
let a = 16
let result = if (a> 0) then a / 10 else 0 #
```

## Pattern matching

``` scala
let readOrInit = match getInteger (this, "someKey") {
    case a: Int => a
    case _ => 0
}
```

Pattern matching is a pattern matching mechanism. Ride allows pattern matching only for predefined types.

Pattern matching in Ride looks the same as in Scala, but the only use case for now is to get the real type from a variable of type Union. Pattern matching can be useful when, as a result of calling a variable, we can get a value with the type `Union`, for example,` Union (Int | Unit) `or it even happens like` Union (Order | ReissueTransaction | BurnTransaction | MassTransferTransaction | ExchangeTransaction | TransferTransaction | SetAssetScriptTransaction | InvokeScriptTransaction | IssueTransaction | LeaseTransaction | LeaseCancelTransaction | CreateAliasTransaction | SetScriptTransaction | SponsorFeeTransaction | DataTransaction) `.

``` scala
let amount = match tx {# tx - current outgoing transaction object in global scope for smart account
  case t: TransferTransaction => t.amount
  case m: MassTransferTransaction => m.totalAmount
  case i: InvokeScriptTransaction => if (i.payment) i.payment.extract (). amount else 0
  case _ => 0
}
```

The above code shows an example of using pattern matching when we want to get the number of tokens transferred in the current transaction from a given account. Depending on the type of transaction, the actual amount of transferred tokens can be stored in different fields. If the transaction is of type `Transfer`,` MassTransfer` or `InvokeScript`, we will take the correct field, otherwise we will get 0.

## pure functions

Ride functions are pure by default, which means that their return values ​​are determined only by their arguments, and their execution has no side effects. In all fairness, there has been a lot of controversy among the Ride developers about the "purity" of the Ride features. The fact is that in Ride there are two variables in the global scope - `height`, which stores the current blockchain height (the number of the current block into which this transaction falls) and` lastBlock`, which stores information about the current block. In theory, the result of a function depends not only on its parameters, but also on the environment (those variables `height` and` lastBlock`), so some will say that functions are "not completely clean" or even "not clean at all".

In any case, Ride is not a pure functional language, since there is also a `throw ()` function that terminates the script at any time. That is, the function may not complete at all, and not just complete with an error, therefore, it will still not work to call the language fully functional.

``` scala
let a = getInteger (this, "key"). extract ()
throw ("I will terminate it!")
let result = if a <0 then
  "a is negative"
else
  "a is positive or 0"
```

In the above example, the script terminates on line 2 with the message `I will terminate it!` And never reaches the `if` expression.

## Annotations / Access Modifiers

Functions can only be defined in a script of type `DAPP` -` {- # CONTENT_TYPE DAPP # -} `. Functions can be without annotations, or with `@ Callable` or` @ Verifier` annotations.

``` scala
func getPayment (i: Invocation) = {
  let pmt = extract (i.payment)
  if (isDefined (pmt.assetId)) then
    throw ("This function accetps waves tokens only")
  else
    pmt.amount
}

@Callable (i)
func pay () = {
  let amount = getPayment (i)
  WriteSet ([DataEntry (i.caller.bytes, amount)])
}
```

Functions with the @Callable annotation can be called from outside the blockchain. To call such functions, you need to send an `InvokeScript` transaction.

Annotations can "bind" some values ​​to a function. In the above example, the `i` variable was bound to the` pay` function and stored all information about the fact of the call (public key, address, payment attached to the transaction, commission, transaction id, etc.).

Functions without annotations ** cannot ** be called "from outside", only the script itself can call them. That is, a `Callable` or a` Verifier` function starts execution, during which the function can be called without annotation.

``` scala
@Verifier (tx)
func verifier () = {
  match tx {
    case m: TransferTransaction => tx.amount <= 100 # no more than 100 tokens can be sent
    case _ => false
  }
}
```

Functions with the @Verifier annotation set the rules for outgoing transactions of a decentralized application (dApp) or smart account. Verifier functions cannot be called externally, but they are executed every time an attempt is made to send a transaction from this account.

Verifier functions must always return a `Boolean` value as a result, depending on whether the transaction enters the blockchain or not.

The verifier function can "bind" a variable that is an object with all fields of the current outgoing (and verified) transaction (`tx` in the example above).

In one script, only one verifier function with the @ Verifier annotation can be defined.

``` scala
@Callable (i)
func callMeMaybe () = {
  let randomValue = getRandomValue ()
  [IntegerEntry ("key", randomValue)]
}

func getRandomValue () = {
  16101997 # quite a random number
}
```

This code will not compile because functions ** without ** annotations must be defined ** before ** functions with annotations.

### Predefined data structures

Ride has many predefined specific data structures for the Waves blockchain, such as: `Address`,` Alias`, `IntegerEngry`,` StringEntry`, `Invocation`,` ScriptTransfer`, `AssetInfo`,` BlockInfo`, etc.

``` scala
let keyValuePair = StringEntry ("someKey", "someStringValue")
```

`StringEntry` is a data structure that describes a key-value pair, as in an account store, where the value is a string.

``` scala
let ScriptTransfer = ScriptTransfer ("3P23fi1qfVw6RVDn4CH2a5nNouEtWNQ4THs", amount, unit)
```

All built-in data structures can be used for type checking and pattern matching.

## Execution result

``` scala
@Verifier (tx)
func verifier () = {
  "Returning some string"
}
```

Expression scripts (with the `{- # CONTENT_TYPE EXPRESSION # -}` directive) must always return a boolean expression along with the `@ Verifier` functions. Depending on this value, the transaction will be accepted (in the case of `true`) or rejected (in the case of` false`) by the blockchain.

`@ Callable` functions can end with 5 types of blockchain changes:

1. Changing the state of the key-value storage, including adding and removing pairs
2. Issue of tokens
3. Re-issue of tokens
4. Transfer of tokens
5. Burning tokens

``` scala
@Callable (i)
func giveAway (age: Int) = {

  let callerAddress = i.caller
  let reissuable = false
  let assetScript = Unit
  let decimals = 0
  let amount = 100
  let nonce = 0
  let newAsset = Issue (assetScript, decimals, "Description here", reissuable, "MyCoolToken", amount, nonce)


  [
    ScriptTransfer (callerAddress, age, unit),
    IntegerEntry ("ageof_" + callerAddress.toBase58String (), age),
    BooleanEntry ("booleanKey", true),
    StringEntry ("stringKey", "somevalue"),
    BinaryEntry ("binaryKey", base58'3P '),
    DeleteEntry ("ScriptTransfer (i.caller, 100, newAsset.calculateAssetId ()),"),
    newAsset,
    ScriptTransfer (i.caller, 100, newAsset.calculateAssetId ()),
    Reissue (base58'81hNyHLFU7Z7PRUeKAfGVPca5CMmFWTxLByHcNAS8i9W ', reissuable, amount),
    Burn (base58'81hNyHLFU7Z7PRUeKAfGVPca5CMmFWTxLByHcNAS8i9W ', amount)
  ]
}
```

Everyone who calls the `giveAway` function will receive as many Waves as he is (the user passes the number of years as an argument), and the dApp will store information about the transfer in its storage, besides this, the script will write several key pairs in the storage of this decentralized application -values ​​(boolean, byte array, string) and will delete the entire pair with the `deleteKey` key. The script will also issue a new token called `MyCoolToken`, send 100 such tokens to the calling account, add 100 tokens with` assetId` equal to `81hNyHLFU7Z7PRUeKAfGVPca5CMmFWTxLByHcNAS8i9W` and burn` 100 tokens with the same assetId`.

Each resulting `Issue` increases the commission for calling such a function by 1 Waves (if a non-NFT token is issued).

The resulting array can contain up to 100 data changes in the storage and 10 operations with tokens (release, burn, send, re-release).

## Exceptions

``` scala
throw ("Here is exception text")
```

The `throw` function will immediately terminate the script execution with the provided text. Нет никаких способов поймать брошенные исключения.

Основная идея `throw` заключается в том, чтобы остановить выполнение и отправить пользователю информативную обратную связь.

```scala
let a = 12
if (a != 100) then throw ("a is not 100, actual value is " + a.toString())
else throw("A is 100")
```

`throw` the function can be used to debug the code when developing dApps, since there is no debugger for Ride yet.

## Execution context

``` scala
{- # STDLIB_VERSION 4 # -}
{- # CONTENT_TYPE EXPRESSION # -}
{- # SCRIPT_TYPE ACCOUNT # -}

let a = this # Current account address
a == Address (base58'3P9DEDP5VbyXQyKtXDUt2crRPn5B7gs6ujc ') # true if the script is executed on an account with a specific address
```

Ride scripts in the waves blockchain can be linked to accounts and tokens (using the directive `{- # SCRIPT_TYPE ACCOUNT | ASSET # -}`), and depending on the `SCRIPT_TYPE` keyword` this` can refer to different entities. For script type `ACCOUNT` -` this` is `Address`

For type `ASSET` -` this` is type `AssetInfo`

``` scala
{- # STDLIB_VERSION 4 # -}
{- # CONTENT_TYPE EXPRESSION # -}
{- # SCRIPT_TYPE ASSET # -}

let a = this # AssetInfo for the current token being operated on
a == assetInfo (base58'5fmWxmtrqiMp7pQjkCZG96KhctFHm9rJkMbq2QbveAHR ') # true if the script is executed for the asset with the specified ID
```

## Macro FOLD <N>

The lack of Turing completeness (we will talk about this in more detail a little later) does not allow Ride to have full-fledged loops, but the language has a macro `FOLD`, which allows you to execute the specified function several times and" collect "the result into one variable.

``` scala
func sum (acc: Int, el: Int) = acc + el
let arr = [1, 2, 3, 4, 5]
let sum = FOLD <5> (arr, 0, sum) # result: 15
```

The parameter in angle brackets (5 in the example above) sets the maximum number of times the `sum` function will be called. Each new call will pass the next element of the `arr` array as an argument. The second parameter of the macro `FOLD` sets the initial value. The sum function takes 2 arguments:

- `acc` - the sum after the previous iteration
- `el` - next element of the array

`sum` will be called with the following parameters:

``` scla
sum (0, 1) # 1
sum (1, 2) # 3
sum (3, 3) # 6
sum (6, 4) # 10
sum (10, 5) # 15
```

`FOLD <N>` is a macro, that is, syntactic sugar. The Ride interpreter does not know anything about `FOLD`, because at the time of compilation,` FOLD` turns into the following code:

``` scala
let result = {
   let size = arr.size ()
   if (size == 0) then acc0 else {
      let acc1 = function (acc0, arr [0])
      if (size == 1) then acc1 else {
         let acc2 = function (acc1, arr [1])
         if (size == 2) then acc2 else {
            let acc3 = function (acc2, arr [2])
            if (size == 3) then acc3 else {
               let acc4 = function (acc3, arr [3])
               if (size == 4) then acc4 else {
                  let acc5 = function (acc4, arr [4])
                  if (size == 5)
                     then acc5
                     else
                       throw ("Too big array, max 5 elements")
}}}}}}
```

Looks much worse than `FOLD <N>`. The `N` parameter must always be an integer greater than 0 and is required. That is, the developer must know the maximum size of the list that will be processed with `FOLD`.

If in `FOLD <N>` you pass an array with dimensions greater than `N`, then an exception will be thrown.

Not all operations possible with other loops can be done with `FOLD`.

## Development process

The development of any application starts with an idea, and decentralized applications are no exception here, however, when it comes to code, it would be good to have a clear sequence of steps, how to launch the idea implemented in the code and make it public. In the case of decentralized apps on Ride, the full lifecycle looks like this:

1. The written code on Ride is compiled into base64 representation. The compiled version of the script can contain meta information about the script (for example, the argument types of `@ Callable` functions are erased at compile time, but can be saved as meta information). There are 2 compilers for Ride - JavaScript and Scala. You can compile the code using different tools - online IDE, REST API nodes, `surfboard` or` ride-js` libraries, an extension for Visual Studio Code. We'll talk about these tools a little later in this section.
2. The compiled script is sent to the blockchain as part of a transaction - SetAssetScript or Issue for smart assets, SetScript for smart accounts and decentralized applications. All these transactions have a `script` field that accepts the compiled code in` base64` representation.
3. After a transaction with a script enters the block, the behavior of the account or asset changes in accordance with what is written in the code.
4. In the case of smart assets, smart accounts and `@ Verifier` functions, the contract code will be executed every time a script account sends a transaction or a transaction with a scripted token is made.
5. In the case of `@ Callable` functions, their execution starts at the moment when any user calls the function using the` InvokeScript` transaction.
6. Updating the script to a new one is possible for both tokens and accounts, if it is not prohibited by the code of the installed script.