# commonware-avs-network-lookup

A Rust library for the Bread Cooperative AVS stack that enables querying and organizing operator and contract data from the Ethereum network, tailored for use with the EigenLayer AVS ecosystem.

## Overview

`commonware-avs-network-lookup` provides functionality to:

- Read AVS deployment configuration and contract addresses from JSON files.
- Connect to Ethereum RPC endpoints to fetch operator registry and state information.
- Retrieve, aggregate, and organize operator data such as addresses, stake, public keys, and network sockets.
- Present operator data grouped by quorum and other metadata, making it useful for monitoring and orchestrating AVS operations.

## Features

- **Configurable**: Reads deployment and contract addresses from environment variables and JSON files.
- **Operator State Retrieval**: Fetches current operator states, including stake and public key information, from Ethereum via the OperatorStateRetriever contract.
- **Quorum Grouping**: Organizes operator data by quorum for easier management and display.
- **Integration**: Designed for seamless use with the Breadchain AVS stack and EigenLayer middleware.

## Usage

### Prerequisites

- Rust toolchain installed (`cargo`)
- Access to Ethereum RPC endpoints (HTTP and WebSocket)

### Environment Variables

- `AVS_DEPLOYMENT_PATH`: Path to the AVS deployment JSON file containing contract addresses.

### Example

```rust
use commonware_avs_network_lookup::network_configuration::EigenStakingClient;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let http_endpoint = "https://mainnet.infura.io/v3/YOUR_PROJECT_ID".to_string();
    let ws_endpoint = "wss://mainnet.infura.io/ws/v3/YOUR_PROJECT_ID".to_string();
    let avs_deployment_path = "./deployment.json".to_string();

    let client = EigenStakingClient::new(http_endpoint, ws_endpoint, avs_deployment_path).await?;
    let operator_states = client.get_operator_states().await?;

    for quorum in operator_states {
        println!("Quorum {}: {} operators, total stake: {}", quorum.quorum_number, quorum.operator_count, quorum.total_stake);
        for operator in quorum.operators {
            println!("  Operator: {:?}", operator);
        }
    }

    Ok(())
}
```

### Deployment JSON Format

Your deployment JSON should contain contract addresses under an `addresses` key, for example:

```json
{
  "addresses": {
    "registryCoordinator": "0x...",
    "blsapkRegistry": "0x...",
    "blsSigCheck": "0x...",
    "counter": "0x..."
  },
  "lastUpdate": {
    "block_number": "12345678"
  }
}
```

## License

See [LICENSE](./LICENSE) for details.

## Contributing

Contributions and feedback are welcome! Please open issues or pull requests on [GitHub](https://github.com/BreadchainCoop/commonware-avs-network-lookup).

---
Maintained by [Breadchain Cooperative](https://github.com/BreadchainCoop).
