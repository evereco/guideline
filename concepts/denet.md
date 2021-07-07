We are pleased to present you the concept of a decentralized Internet, or simply - `DeNet`.
And, we hope that you will like this concept, and we will be able to implement it together with the support of the Free TON Community.

# Concept

So, our concept in a nutshell can be described as placing static inside a smart contract.
Unfortunately, the real `main.ton.dev` network has very critical limitations, for example, such as `16 KB` per message, and we will not be able to fully deploy the static code in the blockchain but we can easily get around it by breaking messages into several blocks of size at `15.99 KB`. In the end, we can create `denet.ton.dev` or `freenet.ton.dev` with our own currency intended for hosting sites, for example `🌎 DeCoins`, and you can get them by exchanging them for `DeFi` for a certain amount of TON Crystal. So, conditionally, we have a network specially configured to work with `DeNet`, their coins have a rate (value).

Now it is necessary to solve the question of how to store this very statics. There are two options. The first is to store the `HTML` code in variables at a smart contract. It seems to us that this approach will not always be advantageous. The second is `TON Storage`. In the foreseeable future, in our opinion, it is not worth waiting for now, but when it appears, we will be able to deploy the statics properly.

Let's go back to `DeNet`. We want to introduce `DHTTP` - `Decentralized HTTP`.

Methods such as, for example, `GET`, `POST`, `PUT`, `DELETE` and others will be written in the code of the contract (interface), which allows us to interact with the internal database (in any form, but still a database).

We will also store an array `routes` where `DeBrowser` (we'll come back later) will get the available routes. But what kind of site can it be if you can't interact with it. We introduce an owners array that can change routing, site configuration, and other metadata. The programmer who writes the code for the `DeNet` site can change and add their own functions for their own needs. We will give an example with our project - **Free TON Ecosystem**. The guest has the right to add his project to the general list by entering the required data. But what happens when he clicks the add button?

The request is sent to the smart contract, to the same `GET` method, from where it gets to another method specified in the `routes` array. Then it goes through authorization (guest or owner), and is passed to other middleware, for example - creating a project (adding information to the mapping), and ultimately the contract sends a response to `DeBrowser`.

Now, as promised, back to `DeBrowser`. This is a special contract that the client deploys. Unfortunately, this is a mandatory procedure to receive a response from a site hosted in `DeNet`. This contract has its own set of methods, which can, for example, display an error message, success message to the user, or execute arbitrary `JavaScript` code (to replace the code in real mode). But in fact, `DeBrowser` is just the same Internet browser, but configured to interact with the blockchain.

Let's not be scattered, and clearly define the methods for each of the contracts.

#### DeNet Endpoint
```
get(string uri) 
post(string uri, string[] array)
head(string uri, string[] array)
put(string uri, string[] array)
delete(string uri, string[] array)
exists(string uri)
redirectMode(address page)
selfDestruct()
getDHTTPVersion() // 1.0, for example
// Other methods writen by developer
```

#### DeBrowser

```
successCallback(address page, string message)
errorCallback(address page, string message)
infoCallback(address page, string message)
updateCallback(address page, string message)
dhttpCodeCallback(address page, uint code) // 404, for example
existsCallback(bool status)
```

#### DeNS (+ search system)

```
getAddress(string domain) //ton.eco, for example, to 0:....
setAddress(address page, string domain)
setMeta(address page, string meta) // 0.... and Free TON Ecosystem
searchAddress(string data) // Free TON Ecosystem -> from meta
removeMeta(address page) // Remove from search system
getAll()
setDesc(address page, string desc) // Set description for site
removeAddress(address page)
inactivePage(address page) // set incative status
setDHTTPVersion(address page, string version)

// Other methods writen by DeNS, for example:
incrementViews(address page)
```
`The shown code is just an example of how we imagine this. Consider this.`

As you may have noticed, we have also described `DeNS` for `DeNet`. It is imperative that these methods be in every contract in the `DeNet` system, otherwise `DeBrowser` will not be able to work. Now we come to safety. After all, each `DeNet-endpoint` must be safe for viewing by the end user. Therefore, we must make a `DeValidator`, which will check the security contract (the existence of any methods) and return the result.

#### DeValidator
```
ableToWork(address page, uint type)

// Other methods, for example:
checkOnBlacklist(address page)
```
`DeValidator` can be public. DeBrowser must also have support for all `DHTTP` versions, having previously obtained it by calling the `DeNet Endpoint` method (`getDHTTPVersion()`), or by obtaining it from `DeNS`.

By the way, a huge advantage of `DeNet` is a one-time payment for deploying a contract, that is, hosting a site. After that, `DeBrowser` users will be able to access your site. But the big disadvantage is that the user will have to pay to deploy `DeBrowser`. At the same time, you do not have to think about authorization by login and password, you will be already authorized by signing messages with `DeBrowser` keys.

# Database


> ⚠ The further description of the concept touches on technical aspects, and requires the user to have basic knowledge of the Solidity programming language.

As with any modern product, it must have a database in any form. There are two options for how you can implement such a database. Let's discuss each of them.

## User case
Let's define a user case, and at the same time make some kind of structure.
```
role
postsCount
balance
blocked
verify
```
Okey, let's go.

## On DeNet-endpoint
This option is the simplest from the programmer's point of view.
```
mapping(address => string) role
mapping(address => uint) postsCount
mapping(address => uint128) balance
mapping(address => bool) blocked
mapping(address => bool) verify

function getBalance(address client) {
	uint128 clientBalance = balance[client];
	return clientBalance
}
```
As you can see, we are using Mapping without structure. This is not the best solution.

## Struct-based
This solution, in our opinion, will be the most optimal
```
struct Client {
	string role;
	uint postsCount;
	uint128 balance;
	bool blocked;
	bool verify;
}
mapping(address => Client) users;

function getBalance(address client) {
	Client user = users[client];
	return user.balance
}
```
As you can see, we are using Mapping with structure.

##  Summary

We recommend that you use a struct-based solution for all tasks.

# End

We sincerely want to develop `DeNet`, but we need funds and experienced staff. If you like this concept, we will definitely continue to develop it. We will supplement this concept, and describe all the little things, and possibly redo our concept in white-paper

Thanks for attention!