# TestNet v3 to v4 Upgrade

On 09/04/2020, TestNet was be upgraded from v3 to v4, with v4 using the latest `und v1.4.1` updates. The following instructions can be used to upgrade your node to the latest
`und v1.4.0` software and v4 genesis.

The v3 database was be exported at height `715000` and used as the `UND-Mainchain-TestNet-v4` genesis.

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

This should output `v1.4.1`

### 4. reset the node data

**Note**: it's good practice to backup the `$HOME/.und_mainchain` directory prior to this

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

