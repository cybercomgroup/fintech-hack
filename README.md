# Cybercom Materials for Fintech Hack

The hacking event is organized at 2017-11-22 in Tampere, Finland.

This workshop is based on Linux (Ubuntu), so if you are running something else, you can adapt the instructions as required, or do this on a Linux virtual machine, for example using VirtualBox.

## Prerequisites

Install go-ethereum (geth): https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum

Make geth sync to the test network (syncing will take about 20 minutes on a normal laptop, so you want to do this first):

    geth --testnet

Geth has synchronized to the current time when new single blocks are received at about one per 10 seconds and the block number is around 2 million (at the time of writing) like so:

    INFO [10-17|15:16:45] Imported new chain segment               blocks=1    txs=7    mgas=0.186   elapsed=24.612ms  mgasps=7.552   number=1888445 hash=484a3d…39bd16
    INFO [10-17|15:16:52] Imported new chain segment               blocks=1    txs=0    mgas=0.000   elapsed=10.174ms  mgasps=0.000   number=1888446 hash=0d6d6e…6e7d30

You should kill the geth process when it has synced, so that it does not conflict with the next instructions.

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

Select localhost 8545 (testrpc):

![Metamask 2](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/metamask2.png "Metamask 2")

Select import existing DEN:

![Metamask 3](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/metamask3.png "Metamask 3")

Copy paste your testrpc passphrase and invent a password:

![Metamask 4](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/metamask4.png "Metamask 4")

You are now logged in to your testrpc local Ethereum blockchain and have a considerable sum of test ether:

![Metamask 5](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/metamask5.png "Metamask 5")

Now Metamask allows Javascript application on Chrome to access this account through web3 API.

## Example token

Install Truffle ([The most popular development framework for Ethereum](https://github.com/trufflesuite/truffle)):

    sudo npm install -g truffle

Then create a new directory for your new FintechToken:

    mkdir fintech-token

Create a Truffle initial project in fintech directory:

    cd fintech-token
    truffle init

You can look at the example code, but it's not a standard token, just an example.

Let's make a proper HumanStandardToken called FintechToken.

First, you need to remove the example token code from the Truffle project leaving only Migrations contract:

    rm test/*
    rm contracts/MetaCoin.sol contracts/ConvertLib.sol

Then we will copy the minimal token base classes (Token.sol, StandardToken.sol, HumanStandardToken.sol) from [ConsenSys examples](https://github.com/ConsenSys/Tokens):

    cp ../ethereum-token/*.sol contracts/

Now these base classes provide the standard ERC 20 token APIs for a contract that inherits them, so that standard Ethereum wallet applications know that this contract is a token contract, and can show balances in a human-friendly way.

Now, let's write some Solidity!

We will create the actual token contract. Create a new file named ``contracts/FintechToken.sol`` with the following content:

    import "./HumanStandardToken.sol";
    
    pragma solidity ^0.4.8;
    
    contract FintechToken is HumanStandardToken {
        function FintechToken() HumanStandardToken(1000, 'Fintech', 2, 'FIN'){
        }
    }

The contract is really simple, as it only calls the base class HumanStandardToken with defined values of 1000 units total, the name of the token "Fintech", two decimals shown by default, and with the token currency symbol of "FIN".

Then we need to define the migrations for deploying the contract. Edit the file ``migrations/2_deploy_contracts.js`` so that it contains only the following (removing the Truffle default contracts)):

    var FintechToken = artifacts.require("./FintechToken.sol");
    
    module.exports = function(deployer) {
      deployer.deploy(FintechToken);
    };

Now, let's compile the contract:

    truffle compile

You should not get any errors as the contract is compiled to the build directory in JSON files containing compiled binaries of the contracts.

Now see again your balance in Metamask in Chrome, it should be 100 ETH, and no tokens:

![DeployLocal 1](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/deploy_local1.png "DeployLocal 1")

Now let's deploy the contract. Note that deployment costs Ethers, but that is not a problem as we are using a local test network with 100 ETH of virtual currency:

    truffle deploy

You will get messages showing the migrations being run, which first deploy the Migrations contract and then your own new contract FintechToken.

Especially important is the following line which shows you the token contract address. Don't lose this (your address will differ from this example):

    FintechToken: 0xd6278722a1bd3c5ffe92320c35a766bc7b7dbc9e

Now check you Metamask account again. You will see your balance has decreased a bit as deploying the contracts consumed gas:

![DeployLocal 2](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/deploy_local2.png "DeployLocal 2")

Now, let's check our token balance. Add a new token:

![DeployLocal 3](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/deploy_local3.png "DeployLocal 3")

Use the contract address you took from the deploy step and paste it to Metamask Add Token address (Note how it fills up the other information automatically as your token implements the HumanStandardToken interface):

![DeployLocal 4](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/deploy_local4.png "DeployLocal 4")

And now you see how you own 10 Fintechs:

![DeployLocal 5](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/deploy_local5.png "DeployLocal 5")

MetaMask does not support sending these tokens to other addresses or people, although that would be possible using some other wallet software.

Congratulations! You have just created your own Ethereum token on your own local machine!

## Advanced: Now to the Testnet

Local testing is one thing, but to really test your contract, you should deploy it to a public Ethereum testnet.

Kill your testrpc with ctrl-c. Next we will use a real Ethereum blockchain, a testnet which only uses test Ethers with no value.

Start geth on testnet with console:

    geth --testnet --rpc console 2>> geth.log

See the value geth uses for its ipc file, you'll need it in the next step, like this:

    datadir: /home/tero/.ethereum/testnet

Start Mist on the test network (use the datadir from the previous step):

    mist --network test --rpc /home/tero/.ethereum/testnet/geth.ipc

You should see something like this when the syncing is done (it takes about 20 minutes on Ropsten testnet):

![Mist started on Ropsten](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/ropsten_mist.png "Mist started on Ropsten")

Get free test Ether from here by copy-pasting in your wallet address (I suggest getting 6, by doing it twice to have enough for token contract creation): http://faucet.ropsten.be:3001/

Now we can also deploy your new Fintech token to the testnet for public testing and use.

First set gaslimit for Truffle, so that it does not try to use unreasonable amounts of gas. Edit the file truffle.js like so:

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*", // Match any network id
          gas: 2900000
        }
      }
    };

Also, you need to unlock your geth account for the local use. Write the following to your geth console:

    personal.unlockAccount(eth.accounts[0], "ThePasswordYouUsedInStartingMist")

Now Truffle can deploy contracts to the testnet. First be sure to wait that Mist has completely synced. Mist shows the sync situation in the bottom left. There should be zero blocks left. Then write:

    truffle deploy

Remember to store the token contract address you get (This is a deployed FintechToken by the author to the Ethereum Ropsten testnet)!

    FintechToken: 0x6fc2d87e14030b4e666626cf2d7fdcf567007ed7

Now you published your first token to Ethereum testnet, and as you were the contract deployer you got the initial ten Fintech tokens!

You can now use Mist to transfer your tokens to anyone else. Note that the person receiving the tokens needs to set up the token into their wallet by adding in the contract address you got from deploying the contract. If everyone uses the same token name and currency symbol, then it might be confusing as there are many different tokens with the same name, but this is not in any way illegal situation. The contract address identifies the token.

Now you can add your token to Mist so that you can verify that you actually got 10 of them, and can freely transfer them to anyone:

Add a token to "watch" in Mist contracts tab and then here:

![Testnet1](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/testnet1.png "Testnet1")

Add the contract address, the other info is autofilled because of the HumanStandardToken interface.

![Testnet2](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/testnet2.png "Testnet2")

Now you see you have 10 Fintech tokens in your testnet wallet!

![Testnet3](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/testnet3.png "Testnet3")

Now you can see your tokens in the wallet and can transfer them like this:

![Testnet4](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/testnet4.png "Testnet4")

If you would want to deploy it in the real public Ethereum blockchain, you would need real Ethers to pay for the contract deployment, but the overall process is identical to the testnet deployment and token use.

## See also

 * https://medium.com/metamask/developing-ethereum-dapps-with-truffle-and-metamask-aa8ad7e363ba
 * https://blog.zeppelin.solutions/the-hitchhikers-guide-to-smart-contracts-in-ethereum-848f08001f05
 * http://truffleframework.com/tutorials/pet-shop
 * https://medium.com/@codetractio/inside-an-ethereum-transaction-fa94ffca912f
 * A nice client-side wallet utility: https://www.myetherwallet.com/
 * A good place to buy Ether from: https://www.kraken.com/
