# Lightweight Beaconchain Explorer

## Introduction:
A Beaconchain explorer is a tool that allows users to view and interact with the data on the Ethereum Beacon Chain. 
It is similar to a blockchain explorer, which allows users to view data on a blockchain such as the current state of 
transactions and blocks - but focussed on exploring the beaconchain.

We often need to use a beaconchain explorer on testnets to allow us to gain some observability into the network, we are usually
just looking for high level metrics into the the network and do not care about deeper indexing or validator performance metrics. This
means the existing gold standard of [beaconcha.in](https://beaconcha.in/) is simply too heavy for most small testnets. We hope to see
a more lightweight version with stripped down features. 

The lightweight explorer could proxy most queries to the client and just store required indexed data in its own database. This 
could make it far simpler to run. Ideally it relies purely on standardized API calls instead of client specific ones, allowing
for us to deploy it irrespective of individual client readiness in testnets.

The explorer additionally doesn't need to store individual validator statistics. We rarely use these in early testnets 
and this would greatly reduce the load on the indexer.

## Features
- Dashboard: The first page of the Beaconchain explorer should be a dashboard that provides a quick overview of the network's status. 
This dashboard should display relevant information such as the current block height, slot height, the number of validators, and the current network epoch. 
Ideally the dasboard also shows participation rate seen over time and the participation rate in each epoch. 
- Search Functionality: The explorer should have the ability to search for a certain slot or epoch. This could be proxied
through to the client and that way the explorer doesn't need to store the entire slot but instead parses it on load. The 
explorer should be able to search through grafitti. 
- Labelling: The explorer should support loading a file that contains validator index mapped to a label. This label should be
displayed instead of the validator index. This would aid greatly in the usability. 
- Slot/Block view: Upon clicking or searching for a certain slot, the explorer should load said slot and parse the values
into a human readable manner.

  
