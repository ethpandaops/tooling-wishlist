# Wrapper for Hive to run local testnets

## Introduction:
Hive is a testing framework that allows us to test multiple parts of the EL/CL stack in a single environment. 
It is an integral testing framework established to assure compliance of various Ethereum client implementations with specification. 
Hive operates by deploying a virtual network of Docker containers, each hosting a unique Ethereum client. 
This infrastructure simulates a private, self-contained setup enabling comprehensive testing without risking actual network disruption. 
Its architecture allows for inter-client comparisons and detection of deviations from the expected protocol behaviors. The 
testing categories encompass consensus tests, ensuring all clients agree on the blockchain state, and p2p tests. 

Since Hive already has the ability to create and deploy clients, it would make sense to add scripts to enable us to create
local testnets using these features. This would allow us to easily create testnets with multiple clients and interoperability
locally before going to the public testnets stage. This is mostly useful for client developers who are already used to the
Hive setup. 

## Features
- Wrapper script, preferably in bash or a popular programming language
- Ability to specify the testnet requirements in a config file

