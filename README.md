<!--- app-name` | Lightning Network Daemon Helm Charts -->

# LND Helm Charts

The Lightning Network Daemon (`lnd`) - is a complete implementation of a
[Lightning Network](https//lightning.network) node.  `lnd` has several pluggable back-end
chain services including [`btcd`](https//github.com/btcsuite/btcd) (a
full-node), [`bitcoind`](https//github.com/bitcoin/bitcoin), and
[`neutrino`](https//github.com/lightninglabs/neutrino) (a new experimental light client). The project's codebase uses the
[btcsuite](https//github.com/btcsuite/) set of Bitcoin libraries, and also
exports a large set of isolated re-usable Lightning Network related libraries
within it.

[Overview of LND](https//github.com/lightningnetwork/lnd/)

                           
## TL;DR

```console
  helm repo add lnd https` |//chrisrun.github.io/lnd-chart/
  helm install lnd lnd/lnd -n lnd --create-namespace
```

This will setup a wallet and store the seed.txt in the pod. The seed.txt file can be removed after the wallet has been initialized.  

## Introduction

This chart bootstraps a [LND](https//github.com/lightningnetwork/lnd/) deployment on a [Kubernetes](https//kubernetes.io) cluster using the [Helm](https//helm.sh) package manager.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+

## Installing the Chart

To install the chart with the release name `lnd`

```bash
$  helm repo add lnd https` |//chrisrun.github.io/lnd-chart/
$  helm install lnd lnd/lnd -n lnd --create-namespace
```

These commands deploy a lightning network node on the Kubernetes cluster in the default configuration.

> **Tip** List all releases using `helm list`


## Uninstalling the Chart

To uninstall/delete the `lnd` deployment

```bash
$ helm delete lnd -n lnd
```

This will not delete the wallet persistent volume claim, nor the unlock password. To remove them, the following two resources need to be removed as well:

```
k delete pvc/lnd -n lnd
k delete secret/lnd -n lnd
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Parameters

### Image Parameters

| Name                      | Description                                     | Value |
| ------------------------- | ----------------------------------------------- | ----- |
| `image.repository` | the image repository to be used | `lightninglabs/lnd`  |
| `image.tag` | the image version | `v0.14.3-beta`  |
| `image.digest` | the image digest, use this if there is no private registry | `""`  |
| `image.pullPolicy` | pull policy | `IfNotPresent`  |


### Lnd Parameters

| Name                      | Description                                     | Value |
| ------------------------- | ----------------------------------------------- | ----- |
| `lnd.alias`    | The alias your node will use, which can be up to 32 UTF-8 characters in length | `MyLightning`  |
| `lnd.address` | Adds an extra public ip to the generated certificate. | `v34.10.10.10`  |
| `lnd.domain`     | the image digest, use this if there is no private registry | `""`  |
| `lnd.revisionHistoryLimit`     | How many revisions should be stored on the cluster to be possible to rollback to | `3`  |
| `lnd.replicas`     | How many replicas of lnd do you want to run | `IfNotPresent`  |

### Disc Storage Parameters

| Name                      | Description                                     | Value |
| ------------------------- | ----------------------------------------------- | ----- |
| `persistence.size`    | The main disk size of the lnd pod | `15G`  |
| `persistence.storageClass` | The storage class used for the disk | `gp2`  |

### Bitcoin Parameters

| Name                      | Description                                     | Value |
| ------------------------- | ----------------------------------------------- | ----- |
| `bitcoin.network` | The bitcoin network, which should be used. Could be | testnet, simnet, regtest or signet |
| `bitcoin.signetchallenge` | Connect to a custom signet network defined by this challenge | `""` | 
| `bitcoin.signetseednode` | Specify a seed node for the signet network instead of using the global default | `""` |
| `bitcoin.node` | Could be as well btcd or bitcoind | `neutrino` |
| `bitcoin.defaultchanconfs` | The default number of confirmations a channel must have before it's considered open. We'll require any incoming channel requests to wait this many confirmations before we consider the channel active. | `3` |
| `bitcoin.defaultremotedelay` | If this is not set, it will scale the value according to the channel size. | `144` |
| `bitcoin.maxlocaldelay` | The maximum number of blocks we will limit the wait that our own funds are encumbered by in the case when our node unilaterally closes | `2016` |
| `bitcoin.minhtlc` | The smallest HTLC we are willing to accept on our channels, in millisatoshi. | `1` |
| `bitcoin.minhtlcout` | The smallest HTLC we are willing to send out on our channels, in millisatoshi. | `1000` |
| `bitcoin.basefee` | The base fee in millisatoshi we will charge for forwarding payments on our channels. | `1000` |
| `bitcoin.feerate` | The total fee charged is basefee + (amount * feerate / 1000000) | `500` |
| `bitcoin.timelockdelta` | The CLTV delta we will subtract from a forwarded HTLC's timelock value. | `40` |
| `bitcoin.dnsseed` | The seed DNS server(s) to use for initial peer discovery. Must be specified as a '<primary_dns>[,<soa_primary_dns>]' tuple where the SOA address is needed for DNS resolution through Tor but is optional for clearnet users. Multiple tuples can be specified, will overwrite the default seed servers. | nodes.lightning.directory, soa.nodes.lightning.directory, lseed.bitcoinstats.com | 

#### Bitcoin Neutrino Parameters

| Name                      | Description                                     | Value |
| ------------------------- | ----------------------------------------------- | ----- |
| `bitcoin.neutrino.connect` | Connect only to the specified peers at startup. This creates a persistent connection to a target peer. This is recommended as there aren't many neutrino compliant full nodes on the test network yet. | `34.123.123.123` | 
| `bitcoin.neutrino.maxpeers` | Max number of inbound and outbound peers. | `10` |
| `bitcoin.neutrino.peers` | Add a peer to connect with at startup. | `[faucet.lightning.community]` |
| `bitcoin.neutrino.banduration` | How long to ban misbehaving peers. Valid time units are {s, m, h}. Minimum 1 second. | `60m` | 
| `bitcoin.neutrino.banthreshold` | Maximum allowed ban score before disconnecting and banning misbehaving peers. | `100` |
| `bitcoin.neutrino.feeurl` | Use top level 'feeurl' option. Optional URL for fee estimation. | `""` |
| `bitcoin.neutrino.assertfilterheader` | Optional filter header in height |hash format to assert the state of neutrino's filter header chain on startup. | `""` |
| `bitcoin.neutrino.useragentname` | Used to help identify ourselves to other bitcoin peers | `neutrino` | 
| `bitcoin.neutrino.broadcasttimeout` | The amount of time to wait before giving up on a transaction broadcast attempt. | `5s` |
| `bitcoin.neutrino.persistfilters` | Whether compact filters fetched from the P2P network should be persisted to disk. | `true` |
| `bitcoin.neutrino.validatechannels` | Validate every channel in the graph during sync by downloading the containing block. This is the inverse of routing.assumechanvalid, meaning that for Neutrino the validation is turned off by default for massively increased graph sync performance. | `false` |

#### Bitcoin Bitcoind Parameters

| Name                      | Description                                     | Value |
| ------------------------- | ----------------------------------------------- | ----- |
| `bitcoin.bitcoind.rpchost` | The host that your local bitcoind daemon is listening on. By default, this setting is assumed to be localhost with the default port for the current network. | `bitcoind.bitcoind.svc.cluster.local` | 
| `bitcoin.bitcoind.rpcuser` | Username for RPC connections to bitcoind. By default, lnd will attempt to automatically obtain the credentials, so this likely won't need to be set (other than for a remote bitcoind instance). | `$RPCUSER` |
| `bitcoin.bitcoind.rpcpass` | Password for RPC connections to bitcoind. By default, lnd will attempt to automatically obtain the credentials, so this likely won't need to be set (other than for a remote bitcoind instance). | `$RPCPASS` | 
| `bitcoin.bitcoind.zmqpubrawblock` | ZMQ socket which sends rawblock and rawtx notifications from bitcoind. By default, lnd will attempt to automatically obtain this information, so this likely won't need to be set (other than for a remote bitcoind instance). | `tcp://bitcoind.bitcoind.svc.cluster.local:28332` |
| `bitcoin.bitcoind.zmqpubrawtx` | ZMQ socket which sends rawblock and rawtx notifications from bitcoind. By default, lnd will attempt to automatically obtain this information, so this likely won't need to be set (other than for a remote bitcoind instance). | `tcp://bitcoind.bitcoind.svc.cluster.local:28333` |
| `bitcoin.bitcoind.estimatemode` | Fee estimate mode for bitcoind. It must be either "ECONOMICAL" or "CONSERVATIVE". | `ECONOMICAL` |
| `bitcoin.bitcoind.pruned-node-max-peers` | The maximum number of peers lnd will choose from the backend node to retrieve pruned blocks from. This only applies to pruned nodes. | `4` |





