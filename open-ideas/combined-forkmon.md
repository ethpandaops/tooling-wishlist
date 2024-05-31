# Combined forkmon

## Introduction:
We currently have a separate forkmon for EL and CLs, ideally we have one webpage we can deploy that will allow us to track
both. The current EL forkmon can be found [here](https://github.com/ethereum/nodemonitor) and the current CL forkmon can be found 
[here](https://github.com/ralexstokes/eth2-fork-mon).

## Features
- Shows the current head of the chain and number of nodes that agree, giving us an overview of the network health
- Shows connection status of the node as well as last ping time
- List of nodes and last agreed upon head for the EL side
- List of bad blocks as reported by nodes
- List of number of peers connected on EL and CL side
- List of current slot and slot root

## Configuration
The forkmon should be configurable with a yaml/toml file. The config file would specify chain parameters as well as endpoints.
The forkmon would be used in the following scenarios:
- Local testnet that needs to show EL as well as CL data
- Public testnet that needs to connect to third party services with authentication, mainly for EL data
- Showcase bad blocks as reported by nodes

An example config file would look like this:
```yaml
config.toml:
    # How often to reload data from the nodes
    reload_interval = "10s"
    # If specified, a http server will serve static content here
    server_address = "0.0.0.0:8080"

    # Shown in the document title, if specified
    chain_name="<NETWORK_NAME>"


    [Metrics]

    enabled = true
    endpoint = "<ENDPOINT>"

    # Local or non third party connection require rpc kind, if EL or CL RPC is missing the data that cannot be retrieved is left empty
    [[clients]]
      name = "<execution-client-name>"
      kind ="rpc"
      el_rpc_url   = "http://<execution-client>:<execution-client-rpc-port>"
      cl_rpc_url   = "http://<consensus-client>:<consensus-client-rpc-port>"
      auth_headers = ["Access-Auth-Id: 1111.access", "Access-Secret: 1111"]
      
    # Third party client configuration requires kind and api key
    # Kind can vary between infura, alchemy or etherscan
    # Recommended to enable rate limit for third party services
    [[clients]]
      name = infura|alchemy|etherscan
      kind = infura|alchemy|etherscan
      api_key = "<API_KEY>"
      endpoint = "<ENDPOINT>"
      rate_limit = 5
```