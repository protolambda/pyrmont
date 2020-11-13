# Pyrmont testnet

A temporary testnet to run v1.0.0 clients as close as possible to mainnet settings.

**Explorer: https://pyrmont.beaconcha.in/**

**Eth2stats: https://pyrmont.eth2.wtf/**

This testnet is:
- Running for a few weeks, likely until mainnet launch.
- Larger than the average testnet, 100.000+ validators, all live.
- Similar to mainnet w.r.t. configuration
- Public, users can try staking setup ahead of mainnet launch

## Config

Chain config file in **[`config.yaml`](./config.yaml)**

Bootnodes in [`bootnodes.txt`](./bootnodes.txt)

Differences with regular mainnet config:
```yaml
# Pyrmont test deposit contract on Goerli (2nd edition, 0x00002009 fork version)
DEPOSIT_CONTRACT_ADDRESS: 0x8c5fecdC472E27Bc447696F431E425D02dd46a8c

# Ethereum Goerli testnet
DEPOSIT_CHAIN_ID: 5
DEPOSIT_NETWORK_ID: 5

MIN_GENESIS_TIME: 1605700800  #  Wednesday, November 18, 2020 12:00:00 PM UTC
GENESIS_DELAY: 432000  # 5 days
GENESIS_FORK_VERSION: 0x00002009
```

Deposit contract on etherscan: [`0x8c5fecdC472E27Bc447696F431E425D02dd46a8c`](https://goerli.etherscan.io/address/0x8c5fecdc472e27bc447696f431e425d02dd46a8c)

Misc. data:
```yaml
deposit_contract_deploy_block_nr: 3743587
deposit_contract_deploy_tx: 0x34a084b9109c120b8dc26efc44a04dc0edda4c8361ab4dc8184b562fe1ea947e

# Fork digest, mixed with zeroed genesis validators root: 
bootnode_fork_digest: 0xd54cf597
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

## F.A.Q.

### What is "Pyrmont"?

Pyrmont is an area in Sydney, a favorite of Sigma Prime (Lighthouse) devs, and location of EDCON 2019, which hosted one of the early Eth2 dev workshops.

### Is Pyrmont a long running testnet?

Not quite. It is planned to run until mainnet launch, and serves a purpose of testing v1.0.0 with mainnet settings, on a large network.
It can then continue, or it may be replaced for a testnet with better UX: long term it is better for users to not have to wait as long in a deposit queue,
 and get removed quicker if offline (most users only try a testnet for a day or two at a time).

### Why the `0x00002009` fork version.

This refers to the 2009 area code of Pyrmont.

### Why is the deposit contract different?

To bootstrap the Pyrmont network, a 100.000 deposits were made, to create validators for the client devs and other parties involved in running the network.
To avoid burning 3.2 million Goerli ETH again (it was done previously, ineffectively), the contract was modified to make the first 100.000 deposits free of charge, for the creator only.
Now that the 100.000 deposits are in, the contract functions the same, and burns Goerli ETH.
The source can is verified on etherscan for your convenience, see [here](https://goerli.etherscan.io/address/0x8c5fecdc472e27bc447696f431e425d02dd46a8c#code)

