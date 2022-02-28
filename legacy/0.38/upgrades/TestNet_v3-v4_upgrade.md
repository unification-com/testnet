# TestNet v3 to v4 Upgrade

On 09/04/2020, TestNet was upgraded from **v3** to **v4**, with v4 using the latest `und` **v1.4.1** updates.
The following instructions can be used to upgrade your node to the latest
`und` v1.4.1+ software and v4 genesis.

The v3 database was exported at height `715000` and used as the `UND-Mainchain-TestNet-v4` genesis.

The _minumum_ required `und` version for `UND-Mainchain-TestNet-v4` is **v1.4.1**

### 1. Halt the node

If the node is currently running on `UND-Mainchain-TestNet-v3` stop the node,
assuming it is being run as a service, for example:

```bash
sudo systemctl stop und
```

Or if the node is not running as a service (i.e. was started using `und start ...`,
and terminal kept open), then for example <kbd>Ctrl</kbd>+<kbd>C</kbd>.

### 2. Update the `und` binary

You can check the method you originally used by running:

```bash
which und
```

If the output is `/usr/local/bin/und`, then the auto install script was _probably_ used.
If the output is similar to `/home/username/go/bin/und`, then it was probably manually installed.

#### 2.1 Auto install script

If the `und` binary was installed using the auto shell script, run:

```bash
curl -sfL https://git.io/JvHZO | sh
```

#### 2.2 Manual installation

If the binary was manually installed by cloning the git repository, and using the `make install` target,
you can either upgrade manually:

```bash
$ rm $GOPATH/bin/und
$ rm $GOPATH/bin/undcli
$ cd /path/to/cloned/mainchain
$ git checkout 1.4.1
$ make install
```

Or, delete the binaries in `$GOPATH` and use the auto-install script:

```bash
$ rm $GOPATH/bin/und
$ rm $GOPATH/bin/undcli
$ curl -sfL https://git.io/JvHZO | sh
```

### 3. check latest version

```bash
und version --long
```

This should output _at least_ `v1.4.1`

### 4. Download the v4 genesis

```bash
curl https://raw.githubusercontent.com/unification-com/testnet/master/latest/genesis.json > $HOME/.und_mainchain/config/genesis.json
```

### 5. reset the node data

**Note**: it's good practice to backup the `$HOME/.und_mainchain` directory prior to this

This will remove the old `v3` chain data, and retain your validation node keys and configuration:

```bash
und unsafe-reset-all
```

### 6. Verify v4 has downloaded

```bash
jq --raw-output '.chain_id' $HOME/.und_mainchain/config/genesis.json
```

Should result in:

```
UND-Mainchain-TestNet-v4
```

**Note**: if you recieve the error `-bash: jq: command not found`, you will need to install `jq`:

For example, on CentOS based systems:

```bash
$ sudo yum install epel-release -y
$ sudo yum install jq -y
```

On Debian:

```bash
sudo apt install jq
```

### 7.  restart the node

Restart your node an join the network.

#### 7.1 `und` as a service

If you have set up the node to run as a service, for example:

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

#### 7.2 manually running `und`

If you are running the node manually, for example:

```bash
und start
```

