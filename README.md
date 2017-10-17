# Cybercom Materials for Fintech Hack

The hacking event is organized at 2017-11-22 in Tampere, Finland.

THIS IS WORK IN PROGRESS! Check back shortly before the event for the final materials.

## Prerequisites

Install go-ethereum (geth): https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum

Make geth sync to the test network (syncing will take about 20 minutes on a normal laptop, so you want to do this first):

    geth --testnet

Geth has synchronized to the current time when new single blocks are received at about one per 10 seconds and the block number is around 2 million (at the time of writing) like so:

    INFO [10-17|15:16:45] Imported new chain segment               blocks=1    txs=7    mgas=0.186   elapsed=24.612ms  mgasps=7.552   number=1888445 hash=484a3d…39bd16
    INFO [10-17|15:16:52] Imported new chain segment               blocks=1    txs=0    mgas=0.000   elapsed=10.174ms  mgasps=0.000   number=1888446 hash=0d6d6e…6e7d30

## Important!

If you have real ether, or any other cryptocurrency for that matter, back up your wallets/private keys well. Almost all security problems with cryptocurrency are about losing access
to one's own funds, not about funds stolen. Remember to test that your backups work.

## Basics

Ethereum is a blockchain that supports developing smart contracts, in addition to having a cryptocurrency (Ether) similar to Bitcoin.

Ethereum development is about developing contracts (with e.g. Solidity), and Dapps (JavaScript), which interact with contracts.

To develop on Ethereum, you will need an RPC server. The RPC server provides access to the Ethereum blockchain to locally running applications.
The RPC server handles your wallets and keys.
There are two main alternatives:

 * testrpc – provides access to a test blockchain on your own machine
 * geth – provides access to public Ethereum blockchain, either on testnet or the proper public network with real ether.

We must always develop a new contract on testrpc first before deploying it to testnet or to the public Ethereum blockchain.

To run Dapps, you need a web3 browser that gives the JavaScript Dapps access to the locally running RPC server through a web3 API. There are two main alternatives:

 * Metamask – A Google Chrome extension that implements the web3 API.
 * Mist – The standard Ethereum browser.

All non-read-only operations on Ethereum contracts create transactions to Ethereum blockchain. All the operations in transactions are executed on all Ethereum nodes on the network.
Executing operations on [24,587](https://www.ethernodes.org/network/1) computers is expensive, and therefore it is paid by expending "gas".
Each operation costs a specific amount of [gas](https://github.com/djrtwo/evm-opcode-gas-costs/blob/master/opcode-gas-costs_EIP-150_revision-1e18248_2017-04-12.csv).
This includes deploying contracts and calling their methods. Read-only contract calls are free, as they can be executed locally only, by just inspecting the blockchain.
The read-only calls do not create transactions to the blockchain.

Sending a transaction to Ethereum blockchain requires setting a gas limit and a gas price.
The gas price is multiplied by the gas spent by running the operations in the transaction, which becomes
the transaction fee for the miners. If the transaction requires more gas than the gas limit, then OutOfGas exception happens, and the contract is not executed, but the gas is spent.

## Let's start!

Install node.js: https://nodejs.org/en/

Install Metamask from Google Chrome store: https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn

Install Mist: https://github.com/ethereum/mist/releases

Install testrpc:

    sudo npm install -g ethereumjs-testrpc

Start testrpc:

    testrpc

Note that testrpc prints out a list of preloaded test accounts with lots of test ether (no real value) to experiment with. The passphrases can be copy-pasted to for example Metamask
to access the test wallets.

Start Chrome and click Metamask icon in the top right corner:

![Metamask 1](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/metamask1.png "Metamask 1")

Select local network (testrpc):

![Metamask 2](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/metamask2.png "Metamask 2")

Select import existing DEN:

![Metamask 3](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/metamask3.png "Metamask 3")

Copy paste your testrpc passphrase and invent a password:

![Metamask 4](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/metamask4.png "Metamask 4")

You are now logged in to your testrpc local Ethereum blockchain and have a considerable sum of test ether:

![Metamask 5](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/metamask5.png "Metamask 5")

## Example token

Install Truffle ([The most popular development framework for Ethereum](https://github.com/trufflesuite/truffle)):

    sudo npm install -g truffle

Minimal token code copied from here: https://github.com/ConsenSys/Tokens

## Now to the Testnet

Start geth on testnet with console:

    geth --testnet --rpc console 2>> geth.log

Start Mist on the test network:

    mist --network test

You should see something like this when the syncing is done (it takes about 20 minutes on Ropsten testnet):

![Mist started on Ropsten](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/ropsten_mist.png "Mist started on Ropsten")

Get free test Ether from here: http://faucet.ropsten.be:3001/

## See also

 * https://medium.com/metamask/developing-ethereum-dapps-with-truffle-and-metamask-aa8ad7e363ba
 * https://blog.zeppelin.solutions/the-hitchhikers-guide-to-smart-contracts-in-ethereum-848f08001f05
 * http://truffleframework.com/tutorials/pet-shop
 * https://medium.com/@codetractio/inside-an-ethereum-transaction-fa94ffca912f
 * A nice client-side wallet utility: https://www.myetherwallet.com/
 * A good place to buy Ether from: https://www.kraken.com/
