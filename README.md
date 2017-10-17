# Cybercom Materials for Fintech Hack

The hacking event is organized at 2017-11-22 in Tampere, Finland.

THIS IS WORK IN PROGRESS! Check back shortly before the event for the final materials.

## Prerequisites

Install [Mist](https://github.com/ethereum/mist/releases).

Make it sync to the test network:

    mist --network test --node-rpc

The '--node-rpc' is needed for the Truffle to access Geth over RPC when it needs to deploy a contract.

You should see something like this when the syncing is done (it takes about 20 minutes on Ropsten testnet):
![Mist started on Ropsten](https://github.com/cybercomgroup/fintech-hack/raw/master/pics/ropsten_mist.png "Mist started on Ropsten")

Get free test Ether from here: http://faucet.ropsten.be:3001/

## Example token

Minimal token code copied from here: https://github.com/ConsenSys/Tokens

Install Truffle ([The most popular development framework for Ethereum](https://github.com/trufflesuite/truffle)):

    sudo npm install -g truffle

Install testrpc (a local Ethereum blockchain for development and testing, an alternative to Mist which uses real Ethereum blockchain):

    sudo npm install -g ethereumjs-testrpc

## See also

 * https://medium.com/metamask/developing-ethereum-dapps-with-truffle-and-metamask-aa8ad7e363ba
