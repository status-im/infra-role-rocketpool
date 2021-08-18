# Description

This role provisions a [RocketPool smart node installer instance](https://github.com/rocket-pool/smartnode-install)
that in turn will install a full-fledged [smart node](https://github.com/rocket-pool/smartnode) using docker-compose.

For general information about RocketPool, please refer to https://medium.com/rocket-pool/rocket-pool-101-faq-ee683af10da9

# Introduction

The smart node installation procedure will create 6 new containers responsible for the various RocketPool components:

```
7278f43c6619   statusim/nimbus-eth2:amd64-v1.0.11             "sh /setup/start-val…"   2 days ago   Up 14 hours                                                        rocketpool_validator
993511abf7e9   rocketpool/smartnode:v1.0.0-beta.1             "/go/bin/rocketpool …"   2 days ago   Up 14 hours                                                        rocketpool_watchtower
526b988a4258   rocketpool/smartnode:v1.0.0-beta.1             "/go/bin/rocketpool …"   2 days ago   Up 14 hours                                                        rocketpool_node
dd235aa746ea   rocketpool/smartnode:v1.0.0-beta.1             "/bin/sleep infinity"    2 days ago   Up 14 hours                                                        rocketpool_api
d63dcecd28bb   statusim/nimbus-eth2:amd64-v1.0.11             "sh /setup/start-bea…"   2 days ago   Up 14 hours   0.0.0.0:9001->9001/tcp, 0.0.0.0:9001->9001/udp       rocketpool_eth2
e8997d22341a   rocketpool/smartnode-pow-proxy:v1.0.0-beta.0   "sh /setup/start-nod…"   2 days ago   Up 14 hours   0.0.0.0:30303->30303/tcp, 0.0.0.0:30303->30303/udp   rocketpool_eth1
```

Config files will be created in `/var/rocketpool`. `/usr/bin/local/rocketpool` will be a simple wrapper
script calling the desired version of the rocketpool binary (installed in /opt/rocketpool) with the necessary
configuration parameters.

The node wallet should be managed manually by executing commands such as `rocketpool wallet restore`
or `rocketpool wallet init`.

# Installation

Add to your `requirements.yml` file:
```yaml
- name: infra-role-rocketpool-smart-node
  src: git+git@github.com:status-im/infra-role-rocketpool-smart-node.git
  scm: git
```

# Configuration

The supported settings are:

```yaml
rocketpool_version: '1.0.0-beta.1'
rocketpool_web3_url: '' # When empty, the smart node will use its own Geth instance
rocketpool_graffiti: '' # Nimbus-compatible graffiti string
rocketpool_eth2_libp2p_port: 9001
rocketpool_eth2_discovery_port: 9001
```

# Requirements

Due to being part of Status infra this role assumes availability of certain things:

* Docker for running containers
* The `iptables-persistent` module

