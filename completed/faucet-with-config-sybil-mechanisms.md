# Faucet with configurable sybil mechanisms

## Done
Information on the tool can be found [here](https://github.com/pk910/PoWFaucet)

## Introduction
We deploy faucets regularly on every testnet, however, we have different sybil requirements based on the publicity of the
network. The current approach involves spinning up different faucets based on the need of the network, but it would be easier
for deployments if we could reuse the same faucet and configure the sybil mechanisms.

We are largely using this [faucet](https://github.com/komputing/FaucETH) and it could be used as a jump off point. 

## Features
- Configurable sybil mechanisms: 
    - IP based
    - Ethereum account nonce based
    - Github based
    - PoW based
    - Captcha based
- Status page to show funds leftover
