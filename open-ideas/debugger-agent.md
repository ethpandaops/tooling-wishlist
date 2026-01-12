# Debugger Agent

## Introduction:
An AI-powered debugging agent that can understand EIP context, interact with Kurtosis devnets, and systematically identify root causes for issues during Ethereum protocol development and testing.

This agent would be integrated into [ethpandaops/ai-cookbook](https://github.com/ethpandaops/ai-cookbook) following agent best practices, providing a powerful tool for debugging EIP implementations across client teams.

## Core capabilities:

### EIP context understanding
- Parse and understand EIP specifications from ethereum/EIPs repo
- Understand the expected behavior changes introduced by an EIP
- Map EIP requirements to client implementation details
- Track which clients have implemented which parts of an EIP

### Kurtosis network integration
- Connect to running Kurtosis devnets and discover available services
- Query client APIs (beacon API, engine API, JSON-RPC) across all nodes
- Collect and correlate logs from all containers
- Monitor network health and identify anomalies in real-time

### Root cause analysis
- Correlate logs across multiple clients (CL + EL pairs)
- Identify divergent behavior between client implementations
- Trace issues back to specific EIP specification ambiguities or implementation bugs
- Generate hypotheses and validate them through targeted tests

### Tooling API integration
Query and correlate data from ethPandaOps tooling:
- **Xatu**: Beacon chain event data and network-wide metrics
- **Tracoor**: Execution traces and block analysis
- **Dora**: Block explorer data and chain state
- **Assertoor**: Test results and assertion failures
- **Grafana/Loki**: Logs and metrics from devnet infrastructure
- **Checkpoint sync endpoints**: State snapshots for analysis

## Agent architecture:

### Workflow capabilities
- Multi-step reasoning for complex debugging scenarios
- Ability to form hypotheses and design experiments to test them
- Memory of previous debugging sessions for similar issues
- Collaborative mode for working with human developers

### Output formats
- Structured bug reports with reproduction steps
- Client-specific issue templates (for filing on client repos)
- EIP clarification requests (for spec ambiguities)
- Test case suggestions for Hive/Assertoor

## Example use cases:

1. **Fork debugging**: "The network forked at slot X on devnet Y. Identify which clients diverged and why."

2**Consensus failure analysis**: "Finality hasn't been reached for 10 epochs. Diagnose the participation issues."

## Integration requirements:

### ai-cookbook integration
- Follow existing agent patterns in the cookbook
- Implement as composable tools that can be used by other agents
- Proper error handling and retry logic
- Structured logging for debugging the debugger

### Best practices to follow
- Stateless tool design where possible
- Clear separation between data gathering and analysis
- Human-in-the-loop for destructive operations
- Token efficient

## Depends on:
- **Loki Kurtosis package**: A starlark definition for Loki, similar to [kurtosis-tech/prometheus-package](https://github.com/kurtosis-tech/prometheus-package)
- **ethereum-package integration**: Integration of the Loki package into [ethpandaops/ethereum-package](https://github.com/ethpandaops/ethereum-package) to collect logs from all clients

## Prior art / related work:
- Existing Claude Code MCP servers in ai-cookbook
- Assertoor test framework
- Kurtosis ethereum-package
- Client debugging commands in the ai-cookbook
- BAL debugger as an open PR in ai-cookbook
