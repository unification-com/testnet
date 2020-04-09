# TestNet v3 to v4 Upgrade

On 09/04/2020, TestNet will be upgraded from v3 to v4, with v4 using the latest `und v1.4.0` updates. The following instructions can be used to upgrade your node to the latest
`und v1.4.0` software and v4 genesis.

The v3 database will be exported at height `715000` and used as the `UND-Mainchain-TestNet-v4` genesis.

**[Set 1](#set-1) - Instructions for Nodes updating PRE/DURING live v4 deployment** (before 2020-04-09T12:00:00Z UTC)  
**[Set 2](#set-2) - Instructions for Nodes updating POST v4 deployment** (after 2020-04-09T12:00:00Z UTC)

## Set 1
## PRE/DURING v4 deployment event (before 2020-04-09T12:00:00Z UTC)

**Note**: This set of instructions is for nodes that are planning their upgrade to happen in concurrence with the live
network upgrade on 09/04/2020, UND-Mainchain-TestNet-v3 block 715000.

For instructions on how to update AFTER this event, please see "**[Set 2](#set-2)**" below

### 1. Halt the node

If the node is currently running on `UND-Mainchain-TestNet-v3` at a height **> `715000`**, stop the node,
assuming it is being run as a service, for example:

```bash
sudo systemctl stop und
```

If the node is currently running on `UND-Mainchain-TestNet-v3` at a height **< `715000`**, stop the node
and restart it with the `--halt-height` flag to gracefully stop the node at the desired height.

```bash
$ sudo systemctl stop und
$ und start --halt-height=715000
```

### 2. Export UND-Mainchain-TestNet-v3 genesis

Once the node has halted, export the `v3` state to a new genesis from height `715000`:


```bash
und export --for-zero-height --height=715000 > $HOME/UND-Mainchain-TestNet-v3_genesis_export.json
```

### 3. Change the Chain ID

Update the new TestNet chain ID:

```bash
cat $HOME/UND-Mainchain-TestNet-v3_genesis_export.json | jq '.chain_id="UND-Mainchain-TestNet-v4"' > $HOME/tmp_genesis.json
```

### 4. Change the Genesis time

Update the new genesis time 

```bash
cat $HOME/tmp_genesis.json | jq '.genesis_time="2020-04-09T12:00:00Z"' > $HOME/tmp_genesis_2.json && mv $HOME/tmp_genesis_2.json $HOME/UND-Mainchain-TestNet-v4_genesis.json
```

### 5. update the `und` binary

```bash
curl -sfL https://git.io/JvHZO | sh
```

### 6. check latest version

```bash
und version --long
```

This should output `v1.4.0`

### 7. reset the node data

This will remove the old `v3` chain data, and retain your validation node keys and configuration:

```bash
und unsafe-reset-all
```

### 8. move the new genesis.json

Move the new `v4` genesis to your node's `$HOME/.und_mainchain/config/` directory:

```bash
mv $HOME/UND-Mainchain-TestNet-v4_genesis.json $HOME/.und_mainchain/config/genesis.json
```

### 9. restart the node

Restart your node an join the network:

```bash
sudo systemctl start und
```

Check the node with:

```bash
$ sudo systemctl status und
```

and:

```bash
$ sudo journalctl -u und --follow
```

## Set 2
## POST v4 deployment (after 2020-04-09T12:00:00Z UTC)

These instructions are for TestNet node operators who will update their nodes after the live upgrade event.

### 1. Halt the node

If the node is currently running on `UND-Mainchain-TestNet-v3` stop the node,
assuming it is being run as a service, for example:

```bash
sudo systemctl stop und
```

### 2. update the `und` binary

```bash
curl -sfL https://git.io/JvHZO | sh
```

### 3. check latest version

```bash
und version --long
```

This should output `v1.4.0`

### 4. reset the node data

This will remove the old `v3` chain data, and retain your validation node keys and configuration:

```bash
und unsafe-reset-all
```

### 5. Download the v4 genesis

```bash
curl https://raw.githubusercontent.com/unification-com/testnet/master/latest/genesis.json > $HOME/.und_mainchain/config/genesis.json
```

### 6. Verify v4 has downloaded

```bash
jq --raw-output '.chain_id' $HOME/.und_mainchain/config/genesis.json
```

Should result in:

```
UND-Mainchain-TestNet-v4
```

### 7.  restart the node

Restart your node an join the network:

```bash
sudo systemctl start und
```

Check the node with:

```bash
$ sudo systemctl status und
```

and:

```bash
$ sudo journalctl -u und --follow
```

