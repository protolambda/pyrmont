# Pyrmont testnet

A temporary testnet to run v1.0.0 clients as close as possible to mainnet settings.

**Explorer: https://pyrmont.beaconcha.in/**

**Eth2stats: https://pyrmont.eth2.wtf/**

This testnet is:
- Short lived, 1 - 2 weeks. Not alongside mainnet.
- Larger than the average testnet, 100.000+ validators, all live.
- Similar to mainnet as can be w.r.t. configuration
- Public, users can try staking setup ahead of mainnet launch

**NOTE: Deposit contract is being re-deployed with new fork version for deposits**

## Config

Chain config file in **[`config.yaml`](./config.yaml)**

Bootnodes in [`bootnodes.txt`](./bootnodes.txt)

Differences with regular mainnet config:
```yaml

# Ethereum Goerli testnet
DEPOSIT_CHAIN_ID: 5
DEPOSIT_NETWORK_ID: 5

MIN_GENESIS_TIME: 1605700800  #  Wednesday, November 18, 2020 12:00:00 PM UTC
GENESIS_FORK_VERSION: TODO
```

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

