# p2p debugger

## Introduction:
Ethereum is a p2p network, this means we should be able to debug this layer to ensure that upgrades happen as expected. It often happens in testnets
that we need to debug peering issues or at the bare minimum look up peerIDs to see which peer sent a message to whom. A tool that can help us
debug and parse this information in a more digestible manner would be greatly useful.

## Features:
- Ability to get peerIDs, ENR and client combination being run (if possible, also validator key range on that host from a config file)
- Ability to visualize peering in a simple manner: get `/eth/v1/node/peers/` from each node and create a visual with annotations of incoming/outgoing connection
- Ability to parse peer score (peer scoring works very differently in all clients, so it might be a high effort low value feature)
- Ability to handle EL and CL peering: Although CL peering undergoes more frequent changes and is vastly more important for debugging

## Configuration:
The tool should be configurable with a yaml/toml file. The config file can take in the following info:
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
ELname = "<execution-client-name>"
CLname = "<consensus-client-name>"    
el_rpc_url   = "http://<execution-client>:<execution-client-rpc-port>"
cl_rpc_url   = "http://<consensus-client>:<consensus-client-rpc-port>"
auth_headers = ["Access-Auth-Id: 1111.access", "Access-Secret: 1111"]
```