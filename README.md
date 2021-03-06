# Description

This role configures [RocketPool smart node](https://github.com/rocket-pool/smartnode) to run using [Systemd services](https://www.freedesktop.org/software/systemd/man/systemd.service.html).

# Introduction

For general information about RocketPool read [this article](https://medium.com/rocket-pool/rocket-pool-101-faq-ee683af10da9).

In simple terms RocketPool provides a "decentralised ETH2 staking protocol". It depends on an Eth1 and Eth2 nodes being available.
This role deploys it's own [nimbus-eth2](https://github.com/status-im/nimbus-eth2) node but depends on an external URL for [go-ethereum](https://github.com/ethereum/go-ethereum).

# Installation

Add to your `requirements.yml` file:
```yaml
- name: infra-role-rocketpool
  src: git@github.com:status-im/infra-role-rocketpool.git
  scm: git
```

# Configuration

A basic configuration would look like:
```yaml
rocketpool_version: '1.0.0'
rocketpool_graffiti: 'my-rocketpool-node'
rocketpool_eth1_network: 'goerli'
# Mandatory
rocketpool_eth2_web3_http_url: 'http://1.2.3.4:8545/'
rocketpool_eth2_web3_ws_url: 'ws://1.2.3.4:8546/'
```

# Management

The smart node installation procedure will create 3 Systemd services:
```
 > systemctl list-units --type=service -a 'rocketpool-*'
  UNIT                          LOAD   ACTIVE SUB     DESCRIPTION
  rocketpool-eth2-node.service  loaded active running Nimbus Beacon Node on mainnet network (stable)
  rocketpool-smart-node.service loaded active running RocketPool smart node daemon
  rocketpool-watchtower.service loaded active running RocketPool watchtower activity daemon
```
Those can be managed with `systemctl` and logs viewed with `journalctl`.

RocketPool binaries are installed in `/opt/rocketpool` and symlinked at `/usr/local/bin`.
```
 > ls -o /usr/local/bin/rocketpool*
lrwxrwxrwx 1 root 58 Oct 27 17:59 /usr/local/bin/rocketpool-cli -> /opt/rocketpool/v1.0.0-pre3/bin/rocketpool-cli-linux-amd64
lrwxrwxrwx 1 root 61 Oct 27 17:59 /usr/local/bin/rocketpool-daemon -> /opt/rocketpool/v1.0.0-pre3/bin/rocketpool-daemon-linux-amd64
```
All config files and data are created in `/data/rocketpool`. It also contains:

* `cli.sh` - Wrapper script which simplifies use of CLI tool.
* `restart.sh` - Wrapper script which restarts all 3 services.

The node wallet can be managed manually by executing commands such as `cli.sh wallet restore` or `cli.sh wallet init`.
