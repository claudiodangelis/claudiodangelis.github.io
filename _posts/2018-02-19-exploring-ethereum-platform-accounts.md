---
layout: post
title: Exploring The Ethereum Platform&#58; Accounts
lang: en
category: ethereum
---

Are you into cryptocurrencies yet? Great, I am not, but after coming across [Ethereum](https://www.ethereum.org) -- a Blockchain app platform -- I felt incredibly intrigued by the concepts of **decentralized applications** so I decided, as a developer, to start exploring it.

<!--more-->

This post is not to be intended as an Ethereum tutorial, but rather as random notes sharing what I learn, as I learn it, the way I learn it, which is basically defining, running and tweaking small tests/proofs as I bump into new concepts.

Since a Blockchain is distributed across nodes of a network, and transactions occur between a sender's and a receiver's addresses, the purpose of my first test is to create a **private network** with a few nodes, create two accounts on it, bring accounts from network node to another, create transactions to move funds from one account to the other.

Why a private network? Because of several reasons like feeling free to mess things up without doing any harm and dealing with things at a very small scale.

This is the plan:

1. Create a network with **3 nodes**
2. Make sure that **node 1** sees **node 2** and **node 3** as its **peers**
3. Create **account 1** on node 2
4. Create **account 2** on node 3
5. Unlock **account 1** from **node 1**
6. **Mine** ether for account 1
7. **Send ether** from account 1 to account 2
8. Make sure that account 2 has **received the ether** sent by account 1


## Create a network with 3 nodes

The network I'm going to create consists of three nodes, with one of them being the _bootnode_ -- the node through which other nodes will join the network and find their peers -- and two of them connecting to the bootnode. For this test, nodes will be implemented as Docker containers connected each other with Docker Compose.

You may want to have a look at the code while reading the remaining part of the post.


I will omit here to post each component of the dockerized network -- you can find the whole test code at my [github account](https://github.com/claudiodangelis/ethereum-test1) -- but one thing that is worth mentioning is how a Blockchain is _started_ or how the very first block of the chain is defined: the **genesis** file. A Blockchain is started by running `geth init` -- `geth` is a Go application that implements the Ethereum protocol -- and passing the _genesis_ file, which is a JSON configuration file that define the initial state of the chain. It is also worth mentioning that in order for nodes to join the _same_ network, all nodes **must** be initialized to the same genesis block.

The genesis file I'm using for this test is the following:

```json
{
    "config": {
        "chainId": 1985,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "difficulty": "4000",
    "gasLimit": "2100000",
    "alloc": {}
}
```

What does this configuration file define?

| Name | Description |
| ---- | ----------- |
| `config.chainId` | This is the ID of the chain (it matches the network id) |
| `config.homesteadBlock`, `config.eip155Block`, `config.eip158Block`  | These value are related to blockchain forking, since we are creating a brand new one, those blocks can be set to `0`  |
| `difficulty`, `gasLimit` | Initial value for mining difficulty and maximum amount of gas that could be used to compute the transaction _(however, mining concepts are left out of this test)_  |


When starts, the bootnode executes the script:

```sh
geth \
    --datadir private \
    init /root/genesis.json

geth \
    --datadir private \
    --nodekey /root/bootnode.key \
    --networkid 1985
```

Which can be read as: initialize a new network using the `genesis.json` file and storing the files in the `./private` directory, then start the network (with ID 1985).

The `--nodekey` parameter refers to the key used by the boot node (1) to generate the enode URL that other nodes (2, 3) will use to join the network.

In this case, the enode URL used by node 2 and 3 will be:

<p><small>
<pre>
enode://eb3a9d0869903f2f233d00856f4840aecf3a0bef1afe9e5b2b336bc010ea19b9ac89c9d57fe4357340dd70b89d57108cc9a7b12510394478a7fc60264351ea9d@$_IP_OF_BOOTNODE_:30303
</pre>
</small></p>


At this point we are ready to spawn the network, if you have [downloaded the code](https://github.com/claudiodangelis/ethereum-test1) just run:

```
docker-compose up
```

After running the command, three containers are started:

- `ethereum_test01_node1` (the boot node)
- `ethereum_test01_node2`
- `ethereum_test01_node3`

## Make sure that node 1 sees node 2 and node 3 as its peers

Once we have started up the nodes, how we can tell that all three are part of the same network?

There are at least two possible answers: count the number of _peers_ a node has, or inspect a detailed list of peers.

Let's count peers first. Run `geth` on node 1, and attach it to the current blockchain to get a JavaScript console:

```
docker exec -it ethereum_test01_node1 \
    geth attach ipc:./private/geth.ipc
```

**Question 1**: why did we use `attach` and not `console`?

If we used the `console` command, `geth` would start a new network, but it would fail because the udp address is already in use by the `geth` instance we used to create our network, so we need to _attach_ `geth` to an existing instance.

**Question 2**: why did we use the `ipc:./private/geth.ipc` argument?

By default, `geth attach` connects to the existing instance using the default data directory, which is `~/.ethereum/geth.ipc`, but we used a custom directory for network data, `./private`, so we need to specify that. The `.ipc` file is a pipe created by a `geth` instance that can be used to interact with it.

After running the command, a JavaScript console is presented to us, so we run the `admin.peers.length` command, and we got the expected result:

```javascript
> admin.peers.length
2
```

Repeat the same test on the two other nodes, and verify that the result of `admin.peers.length` is always `2`.

At this point we are pretty much sure that all nodes are connected to the same network, but what if we want to be even more sure? `admin.peers` returns node's details including the remote address (which is _remote_ from the  _local_ 's node perspective) of the peer, so let's be 100% sure that the two peers we see are those spawned by our docker compose file.

First of all, get nodes'IPs:

{% raw %}
```sh
$ docker inspect -f \
> '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' \
>    ethereum_test01_node1 \
>    ethereum_test01_node2 \
>    ethereum_test01_node3
192.168.96.2
192.168.96.4
192.168.96.3
```
{% endraw %}

Now that we know the IPs of the containers, let's see if they match the information returned by `admin.peers`. In the JavaScript console run this on each node

```js
> admin.peers.map(
    function (p) {
        return p.network.remoteAddress
    }
)
```

Expected results:

```
["192.168.96.3:30303", "192.168.96.4:30303"]
```

```
["192.168.96.2:30303", "192.168.96.4:30303"]
```

```
["192.168.96.2:30303", "192.168.96.3:30303"]
```

Now that we are 100% sure that the network has been correctly created, let's move forward and create two accounts.

## Create account 1 on node 2

Jump on node 2 and create the account:

```
> personal.newAccount("my-secret-passphrase")
"0x5b227defc7cef846e044aa79b36e537cd57a3c1e"
```

The return value of the function is the _address_ of account 1, which means the address that account 1 will send to and receive from. Keep note of the secret passphrase used, because it will be required  later to _unlock_ the account.

This command creates a _keystore_ file that stores your account information. The file is located at the `./private/keystore` directory and the filename is in the format `UTC--<created_at UTC ISO8601>-<address hex>`.

## Create account 2 on node 3

Do the above for account 2 on node 3:

```
> personal.newAccount("my-even-more-secret-passphrase")
"0x3cb1d1aea949200f5825ce23de7d3d34d55acdfd"
```

## Unlock account 1 from node 1

The above commands produced a _keystore_ file, which resides on the node it has been produced onto, which holds accounts information. A few interesting questions start to arise, for example: can I bring my account with me if I move to another node? Could I still be able to use _my_ account on another node of the network? For both questions the answer is yes.

> Note: At this time I'm still not sure how legit the comparison it is, but I see the keystore as a debt card that I can bring with me, the passphrase as the PIN, and nodes as ATMs that are aware of any transactions occurred onto the account tied to my card.

Back to the test, let's see how to use the account 1 (created on node 2) from node 1:

Jump into the container again, then run the `personal.unlockAccount` function by passing the address and the passphrase as arguments:


```js
> personal.unlockAccount("0x5b227defc7cef846e044aa79b36e537cd57a3c1e", "my-secret-passphrase")
Error: no key for given address or file
```

The address (or file we want to read the address from) does not match any keystore file stored into the node 1 (there is no key on such node). How do we fix this? Intuitively, to move the keystore file from one node to another we can simply _copy_ it from source to destination.

Since our nodes are actually docker containers, copying the file will require an intermediate step:

1. Copy the file from node 2 to a local path

```sh
docker cp \
    ethereum_test01_node2:/private/keystore/UTC--2[...]c1e .
```

2. Copy the file from local path to node 1:

```sh
docker cp \
    UTC--2[...]c1e:ethereum_test01_node1:/private/keystore
```

Now run the `personal.unlockAccount` function again on node 1, and see what happens:

```js
> personal.unlockAccount("0x5b227defc7cef846e044aa79b36e537cd57a3c1e", "my-secret-passphrase")
true
```

Now that node 1 has the keystore file, we can _unlock_ the account and use it on the network from that node.

## Mine ether for account 1

Accounts created in the previous steps do not have any ether, meaning that the balance is 0. How do we know that? Just play around with the following `eth` properties and functions on the console:

- `eth.accounts`

    List of all accounts available on the node

- `eth.coinbase`

    The account that stores Ether mined by that node; by default it's the first account (`eth.accounts[0]`)
- `eth.getBalance(address)`

    Returns the balance of `address` expressed in `wei`. Refer to the [documentation](http://ethdocs.org/en/latest/ether.html#denominations) for more information about ether units


**Question**: if there are multiple accounts on the node, how to tell the miner which one should be used to store mined ether? The answer is `miner.setEtherbase("0x123.....")`. From that point on, the value of `eth.coinbase` will be be `0x123....`.

So, how to check that account 1 has balance 0?

```js
> eth.getBalance(eth.coinbase)
0
```

What if we try to send a transaction with 0 balance?

```js
> eth.sendTransaction({from: eth.coinbase, to: "0x3cb1d1aea949200f5825ce23de7d3d34d55acdfd", value: 1000})
Error: insufficient funds for gas * price + value
    at web3.js:3143:20
    at web3.js:6347:15
    at web3.js:5081:36
    at <anonymous>:1:1
```

We get an error about insufficient funds. So let's start the miner from node 1:

```
> miner.start()
```

Since we used a very low value for `difficulty`, letting the miner running for a few seconds is enough to collect enough Ether; before stopping the miner, check the balance again:

```js
> eth.getBalance(eth.coinbase)
30000000000000000000
```

For the sake of the test, miner can be now stopped.

```js
> miner.stop()
true
```

**Question** is this balance of the address available across the network?

Let's get back to node 2 and check it:

```sh
docker exec -it ethereum_test01_node2 \
    geth attach ipc:./private/geth.ipc
```

```js
> eth.getBalance("0x5b227defc7cef846e044aa79b36e537cd57a3c1e")
30000000000000000000
```

## **Send ether** from account 1 to account 2

Now that account 1 has funds, and we tested their availability across the network, let's repeat the transaction of the previous step:

```
> eth.sendTransaction({
    from: eth.coinbase,
    to: "0x3cb1d1aea949200f5825ce23de7d3d34d55acdfd",
    value: 1000
})
"0x1c8c5d4757bb7739f12514b51e66978d605389036de7a3932c8b532b92ba6749
```

The function returned the hash of the transaction, which means that this time funds were sufficient. However, executing transaction requires mining computation, so until we start the miner again the transaction will hang in a _pending_ state. Let's have a look at the pending transactions before starting the miner again:

```js
> eth.pendingTransactions
[{
    blockHash: null,
    blockNumber: null,
    from: "0xd9b862101b7773b2499e5608f7cd0c950500f106",
    gas: 90000,
    gasPrice: 18000000000,
    hash: "0x1c8c5d4757bb7739f12514b51e66978d605389036de7a3932c8b532b92ba6749",
    input: "0x",
    nonce: 0,
    r: "0x87c4217567c1bd95e7f7116f08f9c653d39e91f458a5085e2a5a2af72f633bc2",
    s: "0x5df88b7b3596e08ea30b97ee44aebaa942a1aa01d7943ad0692828a087944b25",
    to: "0x3cb1d1aea949200f5825ce23de7d3d34d55acdfd",
    transactionIndex: 0,
    v: "0xfa6",
    value: 1000
}]
```

The item holds information about the transactions that is about to be executed.

**Question**: is the value of the transaction already available in the receiver account? Of course it is not, and proving it it's easy: just hop on the third node container and run `eth.getBalance(eth.coinbase)`.

## Make sure that account 2 has **received the ether** sent by account 1

Start the miner, then check again both pending transactions on node 1 and account 2 balance on node 3.
Once you got the expected result (i.e.: funds on account 2 on node 3, empty pending transactions) you can stop the miner.


## What's next?

I will continue exploring Ethereum and share my notes, next tests will probably focus on transactions and mining theory.


Also, a huge thanks to [Capgemini's Graham Taylor](https://capgemini.github.io/authors/#author-graham-taylor) for the [Building Private Ethereum Networks With Docker Compose](https://capgemini.github.io/blockchain/ethereum-docker-compose/) article & code, which I'm using as a starting point for my tests.
