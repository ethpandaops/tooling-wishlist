# Grafana Client Dashboards for ethereum-package

## Introduction
Comprehensive Grafana dashboard updates for [ethpandaops/ethereum-package](https://github.com/ethpandaops/ethereum-package) that provide detailed observability into all Ethereum client implementations. These dashboards would give operators and developers deep visibility into client-specific metrics, debug information, and resource utilization across both Execution Layer (EL) and Consensus Layer (CL) clients.

## Proposed dashboards

### Detailed debug metrics dashboards
Per-client dashboards exposing internal debug metrics:
- Memory allocations and GC behavior
- Database read/write operations and latency
- Cache hit rates (state cache, trie cache, etc.)
- P2P layer metrics (peer connections, bandwidth, message rates)
- RPC request latency and throughput
- Internal queue depths and processing times
- Block/state sync progress and performance

### Custom dashboards for each EL client
Tailored dashboards exposing client-specific metrics. Example metrics (not comprehensive):
- **Geth**: Snap sync progress, state healing, leveldb/pebble stats
- **Nethermind**: Pruning stats, beam sync metrics, JSON-RPC analytics
- **Besu**: Bonsai trie metrics, privacy metrics, GraphQL stats
- **Erigon**: Stage sync progress, MDBX stats, TxPool analytics
- **Reth**: ExEx metrics, pipeline stage timing, custom storage backend stats

### Custom dashboards for each CL client
Tailored dashboards exposing client-specific metrics. Example metrics (not comprehensive):
- **Lighthouse**: Slasher DB metrics, block packing efficiency, VC metrics
- **Prysm**: Validator client performance, P2P scoring, state cache analytics
- **Teku**: JVM metrics, REST API performance, validator duty tracking
- **Nimbus**: Light client metrics, ERA file handling, libp2p stats
- **Lodestar**: JavaScript runtime metrics, API latency breakdown, gossip stats

**Note**: Identifying the most useful metrics for each client is a significant undertaking. Each client exposes different metrics with varying naming conventions and semantics. Part of this project involves researching each client's metrics endpoint, understanding what's available, and curating the most valuable metrics for debugging and observability.

### Overview dashboard with resource usage
High-level dashboard for quick health assessment:
- CPU, memory, disk I/O per client container
- Network bandwidth (inbound/outbound) per client
- Disk space usage and growth rate
- Container restart counts and uptime
- Side-by-side comparison of resource usage across client pairs
- Alerts for resource thresholds (high CPU, low disk space, memory pressure)

## Features
- Auto-discovery of deployed clients via service labels
- Consistent color coding and naming across all dashboards
- Templated variables for filtering by client, node, or network
- Pre-configured alert rules for common issues
- Dashboard provisioning via Kurtosis for instant setup
- Export-friendly layouts for reporting

## Metrics sources
Primary data sources for the dashboards:

### Client-native metrics endpoints (preferred)
Each EL/CL client exposes its own Prometheus metrics endpoint:
- Direct access to internal debug and performance metrics
- Client-specific metrics not available elsewhere
- No additional dependencies required

### Ethereum Metrics Exporter (alternative)
[ethpandaops/ethereum-metrics-exporter](https://github.com/ethpandaops/ethereum-metrics-exporter) as a fallback:
- Standardized metrics across all clients via beacon/execution APIs
- Useful for cross-client comparison dashboards
- Fills gaps where client-native metrics are unavailable

### System metrics
- Node exporter for host-level metrics (CPU, memory, disk)

## Integration requirements
- Grafana provisioning in ethereum-package
- Prometheus scrape configs for all client metrics endpoints
- Optional ethereum-metrics-exporter sidecar deployment

## Depends on
- [ethpandaops/ethereum-package](https://github.com/ethpandaops/ethereum-package): Target repository for dashboard integration
- [ethpandaops/ethereum-metrics-exporter](https://github.com/ethpandaops/ethereum-metrics-exporter): Alternative metrics source for standardized cross-client metrics

## Use cases
- Debugging performance issues during devnet testing
- Comparing client implementations under load
- Identifying resource bottlenecks before mainnet deployment
- Monitoring multi-client testnets during hard fork testing
- Capacity planning for validator operations
