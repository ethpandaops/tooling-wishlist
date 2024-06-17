# P2P consensus layer client metrics

## Introduction:
We need to capture metrics from consensus layer clients (lighthouse, nimbus, lodestar, prysm and teku) around the [Gossip](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#the-gossip-domain-gossipsub) and [Req/Resp](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#the-reqresp-domain) domains in the peer to peer layer. Some clients have fully instrumented metrics and others have partial/no instrumentation around these domains. The hope for this issue is to instrument all clients and then be able to compare.

## Metrics

While all of these may not be possible to instrument within the client implementation or underlying libp2p library, these would all be useful to have.

### Gossipsub

[Gossipsub](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#the-gossip-domain-gossipsub) domain related metrics.

#### Messages sent

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of messages sent
- **should** contain the [topic](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#encodings) as a [label](https://prometheus.io/docs/practices/naming/#labels)

#### Messages received (including duplicated)

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of messages received including duplicates
- **should** contain the [topic](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#encodings) as a [label](https://prometheus.io/docs/practices/naming/#labels)

#### Messages received (deduplicated)

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of messages received deduplicated
- **should** contain the [topic](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#encodings) as a [label](https://prometheus.io/docs/practices/naming/#labels)

#### Bytes sent

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of bytes sent
- **should** contain the [topic](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#encodings) as a [label](https://prometheus.io/docs/practices/naming/#labels)
- **should** be the `SSZ-encoded` size of the object(s) in the message

#### Bytes received (including duplicates)

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of bytes received including duplicates
- **should** contain the [topic](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#encodings) as a [label](https://prometheus.io/docs/practices/naming/#labels)
- **should** be the `SSZ-encoded` size of the object(s) in the message

#### Bytes received (deduplicated)

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of bytes received deduplicated
- **should** contain the [topic](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#encodings) as a [label](https://prometheus.io/docs/practices/naming/#labels)
- **should** be the `SSZ-encoded` size of the object(s) in the message

### Req/Resp

[Req/Resp](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#the-reqresp-domain) domain related metrics.

#### Requests sent

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of requests sent
- **should** contain the [protocol ID](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#protocol-identification) as a [label](https://prometheus.io/docs/practices/naming/#labels)

#### Request bytes sent

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of request bytes sent
- **should** contain the [protocol ID](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#protocol-identification) as a [label](https://prometheus.io/docs/practices/naming/#labels)
- **should** be the `SSZ-encoded` size of the object(s) in the message

#### Requests received

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of requests received
- **should** contain the [protocol ID](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#protocol-identification) as a [label](https://prometheus.io/docs/practices/naming/#labels)

#### Request bytes received

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of request bytes received
- **should** contain the [protocol ID](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#protocol-identification) as a [label](https://prometheus.io/docs/practices/naming/#labels)
- **should** be the `SSZ-encoded` size of the object(s) in the message

#### Responses sent

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of responses sent
- **should** contain the [protocol ID](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#protocol-identification) as a [label](https://prometheus.io/docs/practices/naming/#labels)

#### Response bytes sent

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of response bytes sent
- **should** contain the [protocol ID](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#protocol-identification) as a [label](https://prometheus.io/docs/practices/naming/#labels)
- **should** be the `SSZ-encoded` size of the object(s) in the message

#### Responses received

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of responses received
- **should** contain the [protocol ID](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#protocol-identification) as a [label](https://prometheus.io/docs/practices/naming/#labels)

#### Response bytes received

- [counter](https://prometheus.io/docs/concepts/metric_types/#counter) of response bytes received
- **should** contain the [protocol ID](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md#protocol-identification) as a [label](https://prometheus.io/docs/practices/naming/#labels)
- **should** be the `SSZ-encoded` size of the object(s) in the message

## How to get started

Each consensus layer client needs to be looked at and instrumented. This may need to be done on the client layer and/or possibly the libp2p layer.

- [lodestar](https://github.com/ChainSafe/lodestar) - [js-libp2p](https://github.com/libp2p/js-libp2p)
- [teku](https://github.com/Consensys/teku) - [jvm-libp2p](https://github.com/libp2p/jvm-libp2p)
- [lighthouse](https://github.com/sigp/lighthouse) - [rust-libp2p](https://github.com/libp2p/rust-libp2p)
- [prysm](https://github.com/prysmaticlabs/prysm) - [go-libp2p](https://github.com/libp2p/go-libp2p)
- [nimbus](https://github.com/status-im/nimbus-eth2) - [nim-libp2p](https://github.com/vacp2p/nim-libp2p)

## Current client status

Had a quick look at the metrics exported by each client and created the following matrix. Might be errors.

| Metric | Nimbus | Lodestar | Lighthouse | Prysm | Teku |
|--------|--------|----------|------------|-------|------|
| **Gossipsub** Messages sent | ✅ | <span title="gossipsub_rpc_sent_subscription_total: missing topic label and not sure if this is combined" style="cursor:help">⚠️</span> | <span title="gossipsub_topic_msg_sent_counts_total" style="cursor:help">✅</span> | <span title="p2p_pubsub_deliver_total" style="cursor:help">✅</span> | ❌ |
| **Gossipsub** Messages received (including duplicates) | <span title="libp2p_pubsub_received_messages_total: not sure if deduplicated" style="cursor:help">⚠️</span> | <span title="gossipsub_accepted_messages_total: partial topic name and not sure if deduplicated" style="cursor:help">⚠️</span> | <span title="gossipsub_topic_msg_recv_counts_unfiltered_total" style="cursor:help">✅</span> | <span title="p2p_pubsub_duplicate_total" style="cursor:help">✅</span> | <span title="libp2p_gossip_messages_total" style="cursor:help">✅</span> |
| **Gossipsub** Messages received (deduplicated) | <span title="libp2p_pubsub_received_messages_total: not sure if deduplicated" style="cursor:help">⚠️</span> | <span title="gossipsub_accepted_messages_total: partial topic name and not sure if deduplicated" style="cursor:help">⚠️</span> | <span title="gossipsub_topic_msg_recv_counts_total" style="cursor:help">✅</span> | <span title="p2p_message_received_total" style="cursor:help">✅</span> | ❌ |
| **Gossipsub** Bytes sent | <span title="libp2p_network_bytes_total: missing topic label" style="cursor:help">⚠️</span> | <span title="gossipsub_rpc_sent_bytes_total: missing topic label and might include gossipsub and rpc numbers together" style="cursor:help">⚠️</span> | <span title="gossipsub_topic_msg_sent_bytes_total" style="cursor:help">✅</span> | ❌ | ❌ |
| **Gossipsub** Bytes received (including duplicates) | <span title="libp2p_network_bytes_total: not sure if deduplicated and missing topic label" style="cursor:help">⚠️</span> | <span title="gossipsub_rpc_recv_bytes_total: missing topic label and might include gossipsub and rpc numbers together" style="cursor:help">⚠️</span> | <span title="gossipsub_topic_msg_recv_bytes_total: not sure if this is deduplicated" style="cursor:help">⚠️</span> | ❌ | ❌ |
| **Gossipsub** Bytes received (deduplicated) | <span title="libp2p_network_bytes_total: not sure if deduplicated and missing topic label" style="cursor:help">⚠️</span> | <span title="gossipsub_rpc_recv_bytes_total: missing topic label and might include gossipsub and rpc numbers" style="cursor:help">⚠️</span> | <span title="gossipsub_topic_msg_recv_bytes_total: not sure if this is deduplicated" style="cursor:help">⚠️</span> | ❌ | ❌ |
| **Requests** sent | ❌ | <span title="gossipsub_rpc_sent_count_total: missing protocol ID label" style="cursor:help">⚠️</span> | <span title="libp2p_rpc_requests_total: partial protocol ID label" style="cursor:help">⚠️</span> | ❌ | ❌ |
| **Request** bytes sent | ❌ | <span title="gossipsub_rpc_sent_bytes_total: missing protocol ID label and might include gossipsub and rpc numbers together" style="cursor:help">⚠️</span> | ❌ | ❌ | ❌ |
| **Requests** received | ❌ | ❌ | ❌ | <span title="p2p_message_received_total: protocol ID label contains extra data" style="cursor:help">⚠️</span> | ❌ |
| **Request** bytes received | ❌ | ❌ | ❌ | ❌ | ❌ |
| **Responses** sent | ❌ | <span title="gossipsub_rpc_sent_count_total: missing protocol ID label" style="cursor:help">⚠️</span> | ❌ | ❌ | ❌ |
| **Response** bytes sent | ❌ | <span title="gossipsub_rpc_sent_bytes_total: missing protocol ID label and might include gossipsub and rpc numbers together" style="cursor:help">⚠️</span> | ❌ | ❌ | ❌ |
| **Responses** received | ❌ | <span title="p2p_message_received_total: missing protocol ID label" style="cursor:help">⚠️</span> | ❌ | ❌ | ❌ |
| **Response** bytes received | ❌ | <span title="gossipsub_rpc_recv_bytes_total: missing protocol ID label and might include gossipsub and rpc numbers together" style="cursor:help">⚠️</span> | ❌ | ❌ | ❌ |
