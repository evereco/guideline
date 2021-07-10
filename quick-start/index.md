# Fast start
> In this guide, we will show you how easy and simple it is to start developing smart contracts for Free TON.

**Dependencies**
- [Node.js](https://nodejs.org/) (LTS)
- NPM (comes with Node.js)
- [Docker](https://docs.docker.com/desktop/)
- [VC++](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)

### Preparation
All smart contracts in Free TON must be deployed, that is, the contract code must be placed on the blockchain.
Deploying a smart contract necessarily requires a small amount of funds. Since you are just starting development, we will be using the testnet - `net1.ton.dev`.
### Networks

| | Main endpoint address | Currency | Comment |
| ------------ | ------------ | ------------ | ------------ |
| 1 | https://main2.ton.dev | 💎 TON Crystal | Main network |
| 2 | https://net1.ton.dev | ♦ ️ Rubies | Test network |
> Attention, main.ton.dev and net.ton.dev will be unavailable from 12 July 2021.
### List of endpoints

| | Main network | Test network |
| ------------ | ------------ | ------------ |
| 1 | **https://main2.ton.dev** | **https://net1.ton.dev/**
| 2 | https://main3.ton.dev/|https://net5.ton.dev/
| 3 | https://main4.ton.dev/|-
### Getting test rubies
A large number of developers who were just starting to master Free TON had a question "how to get rubies?"

First, you need to install any Google Chrome extension that supports testnet.
As an example: ExtraTON. Then send some rubies to it. There are currently two ways to get them.
- Ask people in the official [chat](https://t.me/freetondevru)
- Use third party services
- - Telegram bot [GiverTON](https://t.me/giverton_bot) (from 1 to 111 rubies, infinitely)
- - [ExtraTON Faucet](https://faucet.extraton.io) (111 rubies, once per hour)

After that, we deploy the wallet. In ExtraTON, you should click on the send icon, and click on "Deploy". Great, you have a wallet with a certain amount of rubies.
### Installing dependencies
To successfully deploy our first smart contract, we need to install the npm package `tondev`:
```
npm install -g tondev
```
After that, go to an empty folder and enter the following command.

```
tondev sol create HelloWorld
```

Great, our contract has been created. Let's deploy it now.

### Compiling the contract
To compile a contract, you must at least have its source code written in the Solidity language. The `tondev` utility has created a file HelloWorld.sol for us, which stores a demo contract.

Let's use this command:

```
tondev sol compile HelloWorld.sol
```

After a short period of time, two files were added to our folder: `HelloWorld.abi.json` and` HelloWorld.tvc`.
#### Deploy the contract
First, we need to generate a key pair
- Public
- - Used to sign messages
- Private
- - Used to interact with secure smart contract methods.
> The private key must never be divulged, as this could result in the loss of all funds.

To generate keys, we use the command:
```
tondev signer generate HelloWorldContract
```

Great, the keys are generated and we can view them with this command

```
tondev signer info HelloWorldContract
```

As we wrote above, you need funds for deployment. If you already have them, then great, let's move on. If not, please go back to the "Getting test rubies" section. We enter the following command

```
tondev contract info HelloWorldContract.abi.json -s testr9
```

Most likely, everything went well, and now you need to copy the address of the future contract opposite `Address:`, but without `(calculated from TVC and signer public)` and spaces.

We send 5 rubies (but not less than 1), and enter the command for deployment:
```
tondev contract deploy HelloWorldContract.abi.json -s testr9 -v 0
```
> Pay attention to the -v 0 switch. Due to some peculiarities of tondev, this switch must always be specified for the deployment to be successful.

> If `Deploying ...` occurs for an infinitely long time without generating errors, it is most likely that the test network is not available at the moment, and it is not possible to use it.
### First contract

Most of you probably have an interest in understanding all aspects of programming smart contracts in Solidity. Yes, if someone did not know, this is the name of a programming language specially created for programming such code. You can also use C and C ++. Solidity is a JavaScript-like language, but strongly typed. We advise you to use this particular language for several reasons:
- Support
- - It's no secret that there are much more programmers who develop in Solidity in Free TON than those who do it in C ++ / C, respectively, for any questions you have, you can be given an answer with an increased chance.
- Easy to learn from scratch
- - Even if you have never encountered programming, then Solidity does not need to go deep into syntax, it is enough to learn a couple of rules, and you are ready to write contracts. And if you already have experience with JavaScript development, then understanding the Solidity syntax will not be difficult for you.
- More examples and documentation
- - Unfortunately, C ++ and C are equipped with fewer examples and documentation than Solidity. There is no "quick start" for these languages.

So, your first contract starts with `HelloWorldContract`. Let's explore it together.
```solidity
// Comments removed to save space
pragma ton-solidity >= 0.35.0;
pragma AbiHeader expire;

contract HelloWorldContract {
    uint32 public timestamp;
    
    constructor() public {
        require(tvm.pubkey() != 0, 101);
        require(msg.pubkey() == tvm.pubkey(), 102);
        tvm.accept();

        timestamp = now;
    }

    function renderHelloWorld () public pure returns (string) {
        return 'helloWorld';
    }

    function touch() external {
        require(msg.pubkey() == tvm.pubkey(), 102);
        tvm.accept();
        timestamp = now;
    }

    function sendValue(address dest, uint128 amount, bool bounce) public view {
        require(msg.pubkey() == tvm.pubkey(), 102);
        tvm.accept();
        dest.transfer(amount, bounce, 0);
    }
}
```

It all starts with specifying `pragma`. These are special "labels" for the compiler. In the example above, the following `pragmas` are set.
```solidity
/*
    We indicate the version for the compiler.
    In this example, it can be greater than or equal to 0.35.0.
    If the compiler used is lower than version 0.35.0 (for example 0.34.0), then it will generate an incompatibility error.
    
    pragma ton-solidity> = 0.35.5; // Compiler version can be higher or equal to 0.35.5
    pragma ton-solidity ^ 0.35.5; // Compiler version must be strictly equal to 0.35.5
    pragma ton-solidity <0.35.5; // Compiler version must be less than 0.35.5
    pragma ton-solidity> = 0.35.5 <0.35.7; // Compiler version can be 0.35.5, but not higher than 0.35.7
*/
pragma ton-solidity >= 0.35.0;

/*
    Special headers for the contract. There are three of them:
    pubkey is the public key with which the message can be signed;
    time - local time at which the message was created;
    expire - the time during which the message should be considered expired.
    The compiler can automatically expose all three headers if you have none specified
*/
pragma AbiHeader expire;
```
Then, there is a description of the contract itself.

```solidity
contract HelloWorldContract {
    //...
}
```

In the future, you will definitely have interfaces. And they must be written after the name of the contract using `is`. Like this:
```solidity
interface HelloWorldInterface {
    function renderHelloWorld() external returns (string);
}
contract HelloWorldContract is HelloWorldInterface {
    //...
}
```
If there are several of them, you must specify each separated by a comma.

Great, we already have the beginning of the contract. But you need to declare variables. Let's define the type of the future variable using a convenient table.

> We recommend that you familiarize yourself with all types.
>
#### Money types

| | Name | Second name | Quantity | Number
| ------------ | ------------ | ------------ | ------------| ------------
| 1  |  nano | nanoton | 1e-9 ton | 0.000000001
| 2  |  micro | microton | 1e-6 ton | 0.000001
| 3  |  milli | milliton | 1e-3 ton | 0.001
| 4  |  ton | Ton | 1e9 nanoton | 1
| 5  |  kiloton | kTon | 1e3 ton | 1000
| 6  |  megaton | MTon | 1e6 ton | 1000000
| 7  |  gigaton | GTon | 1e9 ton | 1000000000


#### Integer types

| | Name | Maximum size | Number of Bits | Comment
| ------------ | ------------ | ------------ | ------------ | ------------
| 1  |  uint   | от 0 до 2^256 | 256 | Not recommended for use
| 2  |  uint8 | от 0 до 2^8 - 1| 8 |
| 3  |  uint16 | от 0 до 2^16 - 1| 16 |
| 4  |  uint32 | от 0 до 2^32 - 1 | 32 |
| 5  |  uint64 | от 0 до 2^64 - 1| 64 |
| 6  |  uint128 | от 0 до 2^128 - 1 | 128 |
| 7  |  uint256 | от 0 до 2^256 - 1 | 256 | Mainly used to store keys.
| 8  |  int   | от -2^256 - 1 до 2^256| 256 | Not recommended for use
| 9  |  int8 | от -2^8 - 1 до 2^8 - 1| 8 |
| 10  |  int16 | от -2^16 - 1 до 2^16 - 1| 16 |
| 11  |  int32 | от -2^32 - 1 до 2^32 - 1 | 32 |
| 12  |  int64 | от -2^64 - 1 до 2^64 - 1| 64 |
| 13  |  int128 | от -2^128 - 1 до 2^128 - 1 | 128 |
| 14  |  int256 | от -2^256 - 1 до 2^256 - 1 | 256 | Mainly used to store keys.

#### Boolean types

| | Name | The values
| ------------ | ------------ | ------------
| 1 | bool | true/false

#### Basic types

| | Name | Description
| ------------ | ------------ | ------------
| 1  |  bytes   | Base64 encoded bytes
| 2  |  string | Convenient presentation of text information without using `bytes`
| 3  |  address | Free TON network address
| 4  |  mapping(Type => Type) | Hash table. For example: `mapping(uint256 => address)`
| 5  |  Type[] | Array. For example: `uint256[]`
| 6  |  struct | Structure


#### Other types

| | Name | Comment
| ------------ | ------------ | ------------
| 1  |  TvmCell | Element ("cell") TVM
| 2  |  TvmSlice | Message payload
| 3  |  TvmBuilder | Message payload constructor
| 4  |  ExtraCurrencyCollection | Balances

The example contract uses the type `uint32`. Pay attention to public.

```
uint32 public timestamp
```

All variables have global visibility, so it is necessary to make the names unique.

Now let's talk about the constructor. This is the most important element of a smart contract after its functionality. When a contract is deployed, the constructor is called. It usually sets some kind of variables. Moreover, they can be installed by the user.

```solidity
constructor() public {
require(tvm.pubkey() != 0, 101);
require(msg.pubkey() == tvm.pubkey(), 102);
tvm.accept();

timestamp = now;
}
```

In the methods of the contract, you can also notice such a function as `require`. It allows you to set mandatory conditions under which the requested method will either be executed or rejected. The first argument must be a `bool` type. Solidity supports `inline-if`, and in the first argument we write as if we wrote in` if`, or, more correctly, a condition. In the second argument, we need to specify an error code that will be returned if the condition is equal to `false`

After `require` comes the` tvm.accept() `method. Probably from the name, you think this function accepts a message. It's like that. But the contract pays a small amount of `gas` every time you send a message to it. Therefore, it is necessary to use this function with caution, and only where it is necessary. It is not worth doing this method in every function, especially if it is not for the user.
``` solidity
tvm.accept();
```

Finally, we set the value above the declared variable. In this case, timestamp.
```solidity
timestamp = now;
```

Now let's move on to the smart contract functionality. To declare a smart contract method, you must use the following syntax:

```function name(arguments) visibility mutability { method body }```

As we can see, the function declaration is not very different from JavaScript, for example. Let's give an example of a function from `HelloWorldContract` and look at its functionality.

```solidity
function sendValue(address dest, uint128 amount, bool bounce) public view {
    require(msg.pubkey() == tvm.pubkey(), 102);
    tvm.accept();
    dest.transfer(amount, bounce, 0);
}
```

Everything we need is here. Name, arguments, visibility and variability. Let's fill in the table.

#### Name - `sendValue`
#### Arguments
|   |  Название | Тип
| ------------ | ------------ | ------------
| 1  |  dest | address
| 2  |  amount | uint128
| 3  |  bounce | bool
#### Visibility
| | Name | Used by | Value
| ------------ | ------------ | ------------ | ------------
| 1  |  public | + | The client can refer to the method
| 2  |  private | - | The client cannot access the method, only other functions.
#### Variability
| | Name | Used by | Value
| ------------ | ------------ | ------------ | ------------
| 1  |  pure | - | Method cannot read and assign a value to variables
| 2  |  view | + | Method can read, but cannot assign a value to variables
| 3  |  default | - | The method can read and assign a value to variables

Excellent. Now let's take a look at the functionality (method body). The familiar require. By the way, it prohibits the execution of the method if it was not signed with the contract keys. `tvm.accept()` is also specified.

Next, we see the translation method. Since each type has its own methods, in this example we see exactly it - a method of the `transfer` type. It transfers to the specified address a certain amount of money specified in the `amount` argument. And `bounce` allows you to return funds if **at the stage of calculations** there was some kind of error.

> This does not protect against loss of funds when sent to a non-existent account. Consider this!

```solidity
    require(msg.pubkey() == tvm.pubkey(), 102);
    tvm.accept();
    dest.transfer(amount, bounce, 0);
```

Wonderful! We have analyzed the contract, and you can change it as you want, and according to your needs. After that, you need to deploy the contract again.

### What's next?

Good question. Next, you have to test your contract. Post it online. After that, write a debot for him. By the way, there will be a separate guideline about this, but for now, write a contract :)

You probably still have questions. This is normal. Feel free to ask them in the official developer chat, and we hope that you will be helped by all means.
Now everything depends on you. You need to come up with an interesting, or implement an existing idea. Or maybe you want to implement our [DeNet](https://github.com/freetonecosystem/guideline/blob/main/concepts/denet.md)  concept ? There are still a lot of unrealized concepts in Free TON. Before you is an unplowed field.

### Conclusion

We hope we were able to help you get started quickly in Free TON. If so, then our goal has been achieved.

Thanks for attention!