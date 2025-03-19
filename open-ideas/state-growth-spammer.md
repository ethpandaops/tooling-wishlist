# State Growth Spammer

## Introduction
A tool to artificially grow the state of Ethereum devets/testnets in a realistic way by simulating mainnet-like transaction patterns. This helps with benchmarking, gas limit discussions, and load testing by creating real-world I/O patterns and state access complexity.

**NOTE: Consider integration into [spamoor](https://github.com/ethpandaops/spamoor) which already contains a lot of the base logic.**

## Features
- Simulate mainnet-like transaction patterns:
  - DeFi interactions (pools, trades, lending)
  - NFT minting and trading
  - Token transfers and approvals
  - Contract deployments
- Configurable state growth targets:
  - By size (GB of state)
  - By number of accounts
  - By number of storage slots
- Realistic transaction distribution:
  - Based on historical mainnet analysis
  - Configurable time compression
  - Smart contract interaction patterns
- Built-in patterns for popular protocols:
  - Uniswap-style AMMs
  - Lending protocols
  - NFT marketplaces
  - ERC20/721 token interactions
- Monitoring and metrics:
  - State growth over time
  - Transaction success rate
  - Gas usage patterns
  - Storage slot access patterns
- Integration with existing tools:
  - Kurtosis
  - Ansible collection

## Configuration
The tool accepts a YAML config file:
```yaml
network:
  rpc_url: "http://localhost:8545"
  chain_id: 1337
  private_keys: []  # List of funded accounts to use

growth_targets:
  state_size_gb: 100
  num_accounts: 1000000
  storage_slots: 5000000

patterns:
  defi:
    enabled: true
    weight: 0.4  # 40% of transactions
    protocols:
      - type: "uniswap_v2"
        pairs: 100
        swaps_per_block: 5
      - type: "lending"
        markets: 10
        
  nft:
    enabled: true
    weight: 0.2
    collections: 50
    mints_per_block: 2
    
  erc20:
    enabled: true
    weight: 0.3
    tokens: 200
    transfers_per_block: 10
    
  contract_creation:
    enabled: true
    weight: 0.1
    contracts_per_hour: 50

execution:
  blocks_per_second: 1
  max_pending_txs: 1000
  gas_price_strategy: "market"  # or "fixed"
  priority_fee: "1.5gwei"
```

## Integration
The tool can be:
- Run standalone for quick state growth
- Integrated into test frameworks
- Embedded in devnet deployment tools

## Use Cases
- Benchmarking client performance with realistic state sizes
- Testing gas limit increases under real-world conditions
- Validating state access optimizations
- Simulating mainnet-like load on testnets
- Testing state pruning and sync strategies