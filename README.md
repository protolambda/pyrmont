# Pyrmont testnet

A temporary testnet to run v1.0.0 clients as close as possible to mainnet settings.

**Explorer: https://pyrmont.beaconcha.in/**

**Eth2stats: https://pyrmont.eth2.wtf/**

This testnet is:
- Short lived, 1 - 2 weeks. Not alongside mainnet.
- Larger than the average testnet, 100.000+ validators, all live.
- Similar to mainnet as can be w.r.t. configuration
- Public, users can try staking setup ahead of mainnet launch

WARNING:
- This testnet uses mainnet fork version (see reasons below)
- DO NOT REUSE THE SAME KEYS OF A TESTNET ON MAINNET


## Config

Chain config file in **[`config.yaml`](./config.yaml)**

Bootnodes in [`bootnodes.txt`](./bootnodes.txt)

Differences with regular mainnet config:
```yaml
DEPOSIT_CONTRACT_ADDRESS: 0x2c539a95d2a3f9b10681D9c0dD7cCE37D40c7B79

deposit_contract_deploy_block_nr: 3713500
deposit_contract_deploy_tx: "0xf98900cca43d5a1ca8e74c8ad24b53a1f8a26d89f51980964a632eb45f219171"

# Ethereum Goerli testnet
DEPOSIT_CHAIN_ID: 5
DEPOSIT_NETWORK_ID: 5

MIN_GENESIS_TIME: 1605700800  #  Wednesday, November 18, 2020 12:00:00 PM UTC
```

**Note that it uses mainnet fork version**: `GENESIS_FORK_VERSION: 0x00000000`
The testnet is not meant to run long-term, and mainnet fork version was chosen because:
- Any deposits made with Pyrmont config, if bypassing safety measures, will at least have a valid mainnet signature. Funds will not get lost if keys are kept.
- Launchpad / deposit CLI explicitly checks against network differences, see https://github.com/ethereum/eth2.0-deposit-cli/pull/157 
- The genesis validators root is mixed with the fork version, for a unique fork digest, used for everything but deposits and bootnodes. This avoids most issues.
  - You cannot get slashed by reusing keys. Not recommended though, use different keys!
- The network is not meant to run alongside mainnet, but rather just as preparation by being as similar as possible

[`0x2c539a95d2a3f9b10681D9c0dD7cCE37D40c7B79` on etherscan](https://goerli.etherscan.io/address/0x2c539a95d2a3f9b10681D9c0dD7cCE37D40c7B79)

Misc. data:
```yaml
# Fork digest, mixed with zeroed genesis validators root: 
bootnode_fork_digest: TODO
# curl localhost:4000/eth/v1/beacon/genesis
# The genesis time, after waiting for a good eth1 candidate block etc.
actual_genesis_time: TODO
genesis_validators_root: TODO
# curl localhost:4000/eth/v1/beacon/states/genesis/root
genesis_state_root: TODO
# curl localhost:4000/eth/v1/beacon/blocks/genesis/root
genesis_block_root: TODO
```

## Eth2stats

Eth2stats dashboard: https://pyrmont.eth2.wtf

Example to participate:
```
docker run -d --restart always --network="host" \
--name eth2stats-client \
-v ~/.eth2stats/data:/data \
alethio/eth2stats-client:latest run \
--eth2stats.node-name="example node" \
--data.folder="/data" \
--eth2stats.addr="grpc.pyrmont.eth2.wtf:8080" --eth2stats.tls=false \
--beacon.type="v1" \
--beacon.addr="http://localhost:5052" \
--beacon.metrics-addr="http://localhost:5054/metrics"
```

More info about eth2stats in the [eth2stats-client repo](https://github.com/Alethio/eth2stats-client/blob/master/README.md)


## Client configuration

Work in progress. Not all clients support the expected flags yet.

### Prysm

Run the `v1.0.0-rc.0` branch

Or use docker images:

```yaml
beacon_image_name: gcr.io/prysmaticlabs/prysm/beacon-chain:pyrmont
validator_image_name: gcr.io/prysmaticlabs/prysm/validator:pyrmont
```

#### Beacon node

```
  --pyrmont
```

#### Validator

```
  --pyrmont
```


### Lighthouse

Use the [`pyrmont`](https://github.com/sigp/lighthouse/pull/1874) branch (until that PR is merged into master).

#### Beacon node

```
  --testnet pyrmont
```

#### Validator
```
  --testnet pyrmont
```

### Teku

#### Beacon node & validator

```
  --network=pyrmont
```

### Nimbus

Run `devel` or `master` branch

#### Beacon node

Prepare a binary with insecure/experimental features enabled:
```shell script
make -j8 LOG_LEVEL="TRACE" NIMFLAGS="-d:insecure" beacon_node
```

Use the `pyrmont` network option.

```
  --network=pyrmont
```

#### Validator

No configuration necessary, fork version etc. are loaded from the beacon node.

Prepare a binary with insecure/experimental features enabled:
```shell script
make -j8 LOG_LEVEL="TRACE" NIMFLAGS="-d:insecure" validator_client
```

