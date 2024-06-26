# TestNet Network Upgrade: 3-keyleth

There are two possible methods for upgrading:

1. Automatically, using Cosmovisor (recommended)
2. Manually

## Automatically upgrade from und v1.7.x to v1.8.x using Cosmovisor

**IMPORTANT:** This guide assumes the reader has implemented the required changes outlined in
[Using Cosmovisor with und: Quick Start](./cosmovisor.md) and migrated their services before using this guide.

### Configuring Cosmovisor

The following can be implemented well in advance of the actual upgrade occurring, which will allow
for a completely automated upgrade.

**IMPORTANT** During the upgrade, `cosmovisor` will automatically do a full backup of the `.und_mainchain/data`
directory. Ensure your host has adequate disk space to accommodate the backup. This may add significant time
to the upgrade process, and as such, the process may take up to 30 minutes before the node comes back online.

1. Create the Cosmovisor `3-keyleth` upgrade directory

This will be dependent on how you configured `cosmovisor`, and your actual `.und_mainchain` path

```bash
mkdir -p $HOME/.und_mainchain/cosmovisor/upgrades/3-keyleth/bin
```

2. Download the latest `und` v1.8.x and add to Cosmovisor's `upgrades/3-keyleth/bin` directory

```bash
mkdir -p $HOME/tmp/3-keyleth && cd $HOME/tmp/3-keyleth
wget https://github.com/unification-com/mainchain/releases/download/v1.8.2/und_v1.8.2_linux_x86_64.tar.gz
tar -zxvf und_v1.8.2_linux_x86_64.tar.gz
mv und $HOME/.und_mainchain/cosmovisor/upgrades/3-keyleth/bin/
```

The directory structure for `$HOME/.und_mainchain/cosmovisor` should now look as follows:

```
.
├── current -> /path/to/.und_mainchain/cosmovisor/genesis
├── genesis
│   └── bin
│       └── und
├── UND_COSMOVISOR_ENV
└── upgrades
    └── 1-ibc
        └── bin
            └── und
    └── 2-grog
        └── bin
            └── und
    └── 3-keyleth
        └── bin
            └── und
```

3. Check the version is 1.8.2

```bash
$HOME/.und_mainchain/cosmovisor/upgrades/3-keyleth/bin/und version --log_level ""
```

That's it! Once the upgrade height specified in the governance proposal is reached, Cosmovisor and the `upgrade`
module will handle the rest automatically.

### Cosmovisor Upgrade process overview

Briefly, at the upgrade height, Cosmovisor will automatically:

1. Stop the `und` v1.7.x binary
2. Backup `.und_mainchain/data` to `.und_mainchain/data-backup-YYYY-M-DD`
3. Reconfigure itself to use `und` v1.8.x
4. Restart `und` using the new version

## Manual upgrade (not recommended)

The alternative to implementing Cosmovisor is to manually upgrade the binary. Once the upgrade height specified in the
governance proposal is reached, the `upgrade` module will automatically halt the node via a `panic`. The node operator
will then need to:

1. Stop the `und` v1.6.x binary, via `systemd` or their chosen method
2. Backup the `und_mainchain/data` directory
3. Download and install the latest `und` v1.8.x, replacing the old v1.7.x binary (for example in `/usr/local/bin`)
4. Restart the `und` binary, via `systemd` or their chosen method.

Since the process involves manual intervention, monitoring and execution, the process may take longer.
