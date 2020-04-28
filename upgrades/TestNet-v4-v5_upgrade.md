# TestNet v4 to v5 Upgrade

On 28/04/2020 in preparation for MainNet deployment, TestNet was rebuilt from the ground up using the latest `und` software updates. Account data and associated funds have been retained in the `TestNet-v5` genesis so any keys and accounts can still be used in `v5`. However, TestNet validator nodes will need to re-register and self-delegate by running the `create-validator` command.

Account data was exported at `TestNet-v4` height 263,000.

The _minumum_ required `und` and `undcli` version for `UND-Mainchain-TestNet-v4` is **v1.4.3**

### 1. Halt the node

If the node is currently running on `UND-Mainchain-TestNet-v4` stop the node,
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
sh -c "$(curl -sfSL https://git.io/JvHZO)"
```

#### 2.2 Manual installation

If the binary was manually installed by cloning the git repository, and using the `make install` target,
you can either upgrade manually:

```bash
$ rm $GOPATH/bin/und
$ rm $GOPATH/bin/undcli
$ cd /path/to/cloned/mainchain
$ git checkout 1.4.3
$ make install
```

Or, delete the binaries in `$GOPATH` and use the auto-install script:

```bash
$ rm $GOPATH/bin/und
$ rm $GOPATH/bin/undcli
$ sh -c "$(curl -sfSL https://git.io/JvHZO)"
```

### 3. check latest version

```bash
und version --long
```

This should output _at least_ `v1.4.3`

### 4. Download the v5 genesis

```bash
curl https://raw.githubusercontent.com/unification-com/testnet/master/latest/genesis.json > $HOME/.und_mainchain/config/genesis.json
```

### 5. reset the node data

**Note**: it's good practice to backup the `$HOME/.und_mainchain` directory prior to this

This will remove the old `v4` chain data, and retain your validation node keys and configuration:

```bash
und unsafe-reset-all
```

### 6. Verify v4 has downloaded

```bash
jq --raw-output '.chain_id' $HOME/.und_mainchain/config/genesis.json
```

Should result in:

```
UND-Mainchain-TestNet-v5
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

Restart your node and join the network.

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

### 8. Become a validator

**Important**: Wait for your node to fully sync with the network before continuing.

Using the same account, validator public key and self-delegate amount you used to register your validator node on the previous TestNet version, re-run your `create-validator` command, e.g.:

```bash
undcli tx staking create-validator \
  --amount=STAKE_IN_NUND \
  --pubkey=NODE_TENDERMINT_PUBLIC_KEY \
  --moniker="YOUR_EV_MONIKER" \
  --website="YOUR_WEBSITE_URL" \
  --identity=16_DIGIT_KEYBASE_IO_ID \
  --details="NODE_DESCRIPTION" \
  --security-contact="SECURITY_CONTACT_EMAIL" \
  --chain-id=CHAIN_ID \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --gas="auto" \
  --gas-prices="0.25nund" \
  --gas-adjustment=1.5 \
  --from=SELF_DELEGATOR_ACCOUNT
```

