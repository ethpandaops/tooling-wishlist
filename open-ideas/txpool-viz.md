# Mempool Visualizer

## Introduction:
A tool to visualize and compare transaction pools across different Ethereum execution clients. This helps debug transaction propagation issues, analyze mempool behaviors during attacks, and understand differences in transaction acceptance policies between clients.

## Features
- Connect to multiple EL RPC endpoints simultaneously
- Poll txpool_content from each node at configurable intervals
- Visualize which transactions exist in which mempools:
  - Venn diagram view showing overlap between different nodes (or some other visual)
  - Timeline view showing when txs appear in each mempool
  - Transaction details on hover/click
- Filter and group transactions by:
  - Gas price/priority fee
  - Nonce ranges
  - Address patterns
  - Transaction type (legacy/EIP1559/blob/7702)
- Export data for offline debugging and sharing
- Optional websocket support for instant updates
- Basic transaction validation checks to highlight potentially invalid txs (likely import geth/other client as a lib)

## Configuration
The tool should accept a YAML config file specifying:
```yaml
yaml
endpoints:
    - name: "geth-1"
      url: "http://localhost:8545"
      auth_headers: {} # Optional auth headers
    - name: "erigon-1"
      url: "http://localhost:8547"
polling:
    interval: "5s" # How often to poll mempools
    timeout: "2s" # RPC timeout
filters:
    min_gas_price: "1gwei" # Optional filters
```

## Integration
Two potential approaches:
- Standalone tool that is added to kurtosis and optionally ansible for devnets
- Built into dora

There are pro/cons for both, but likely either approaches would end up being useful for devs.