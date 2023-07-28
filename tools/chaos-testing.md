# Chaos Testing
Note: this is actively being worked on, reach out to know more
## Introduction
Chaos testing, also known as chaos engineering, is a method of testing the robustness and resilience of a system by 
intentionally introducing faults, errors, or disruptions into the system's operations. 
The main idea behind chaos testing is to proactively expose and address system weaknesses before they cause significant 
issues, especially in production environments.

Chaos testing frameworks are quite robust and easily configurable in the CNCF (CLoud Native Computing Foundation) ecosystem.
We can leverage our existing tooling of either [Kurtosis](https://www.kurtosis.com/) or [Ethereum Helm Charts](https://github.com/ethpandaops/ethereum-helm-charts/)
to spin up the testnet in kubernetes and then use tools like [Litmus](https://litmuschaos.io/), [Chaos Mesh](https://chaos-mesh.org/)
or [Chaos Blade](https://chaosblade.io/en/) to inject faults into the system.

## Features
- Assert the testnet is alive and well before injecting faults
- Define faults to inject as a series of config files (tests)
- Focus on p2p layer to begin with, especially latency changes
- Assert if testnet is in a healthy state after injecting faults
- If not, collect logs for test run
- Ability to run tests in parallel (if possible with k8s namespaces)
- Ability to trigger individual tests
- Run nightly or at a regular interval

Note: Kurtosis already supports a kubernetes backend, so it might be the easier approach. That would allow more time to be
spent on chaos testing. The downside is that we are tied to the bugs and update cycle of kurtosis of course. 