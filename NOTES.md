### Dump roles

```
pg_dumpall --roles-only > roles.sql
```

### Restore

```
psql -f roles.sql postgres
```

### Copy folders in a disabled database

```
cp -rap data data_file_bck
```

- copy - copy files/directories
- -r - recursive (copy directories and their contents)
- -a - archive: preserves mode, ownership, timestamps, symlinks, and copies recursively (equivalent to -dR --preserve=all inGNU cp).
- -p - preserve mode, ownership, timestamps (redundant because -a already preserves these).

> [!NOTE]
> -a makes -r and -p unnecessary, but using them together is harmless

### Lists all systemd unit files and shows only the lines that contain "postgresql" 

```
systemctl list-unit-files | grep postgres
```
### Prints the full systemd unit file for postgresql-17.service

```
systemctl cat postgresql-17.service
```

### Creating a new, empty, encrypted database

> [!NOTE]
> When we create a new DATA directory, we generate a new TDE key, which is stored in an encrypted JSON file. The encrypted key is protected using a key stored in Vault. When we rotate passwords, we rotate the key in Vault, not the TDE key.


### Creating a directory with configuration for PGEE

Create a direcotry

```
mkdir -p /etc/pgee
```

Set proper directory permissions

```
chown postgres: /etc/pgee
```

### Create a file containing environment variables 

Create a file

```
touch /etc/pgee/pgee_service.env
```

Add the required values to this file:

```
VAULT_ADDR=https://<vault address>:8200
VAULT_TOKEN=<vault token>
```

### Test connectivity to HashiCorp Vault and the VAULT_TOKEN settings

```
set -a
source /etc/pgee/pgee_service.env
pgee_key_manager -vault -vault-mount tde-cybertec -vault-path /zwsbii/nonprod/<hostname>/data
```

### Length of the key

When generating a TDE key, we must select a key lenght - e.g., 128, 192, ora 256 bits.

For a 256-bit key, the proper command is shown below. 

```set -a
source /etc/pgee/pgee_service.env
/usr/pgsql-15/bin/initdb -K 'pgee_key_manager -key-bits 256 -vault -vault-mount tde-cybertec -vault-path zwsbii/nonprod/<hostname>/data' --key-bits 256
```

For a standard key length of 128 bits, the correct command is shown below (you don't need to specify the key length because 128 bits is the default).

```
/usr/pgsql-15/bin/initdb -K 'pgee_key_manager -vault -vault-mount tde-cybertec -vault-path zwsbii/nonprod/<hostname>/data'
```

### Verify the configuration file to ensure TDE is configured properly. 

In the configuration file (postgresql.conf)

```
cat /var/lib/pgsql/15/data/postgresql.conf | grep -E 'encryption_key|bits'
```

You should see output like this:

>encryption_key_command = 'pgee_key_manager -key-bits 256 -vault -vault-mount tde-cybertec -vault-path zwsbii/nonprod/hostname/data'

Also display ***tdekey.json*** to verify the configuration.

```
cat /var/lib/pgsql/15/data/tdekey.json
```

It should look like this:

>{"method":"vault","encrypted_key":"1W73lJ6UYSm31owVNdyB0hbFz8X6fNyn+4HaxqwhIasdfasdfasqwASDFasreCYhSOHXkqb7TDZsd1HY1qPCxdjSCN/1pTHhjV2sycWQMIU=","version":3}

### Check the variables in the systemd configuration file

```
systemctl edit postgresql-15.service
```

You should see output like this:

>[Service]

>EnvironmentFile=/etc/pgee/pgee_service.env

>ExecStart=

>ExecStart=/usr/pgsql-15/bin/postgres -D ${PGDATA}


Reload the systemd manager configuration 

```
systemctl daemon-reload
```

### Verify the process database owner

Check the owner of the process database. If the owner is not 
***postgres***, change it to the ***postgres*** user. This can be set in
***ExecStart***. 


Check the following in context:
    1. Whether the user is ***postgres***
    2. Whether we are in pg_data
    3. That the binary is ***postgres***, not ***postmaster***. 

To do this run command below:

```
systemctl show postgresql-15.service| grep Env
```

You should see output like this:

>Environment=PGDATA=/var/lib/pgsql/15/data/ PG_OOM_ADJUST_FILE=/proc/self/oom_score_adj PG_OOM_ADJUST_VALUE=0

>EnvironmentFiles=/etc/pgee/pgee_service.env (ignore_errors=no)


### Disabled the previous database instance. 

```
systemctl stop postgresql.service
```

```
systemctl disable postgresql.service
```

### Verify the Vault configuration

If Vault is installed, verify the Vault configuration.

```
<pre>vault kv get -mount tde-cybertec zwsbii/nonprod/hostname/data</pre>
```

### Enable and start the new database instance

```
systemctl start postgresql-15.service
```

```
systemctl enable postgresql-15.service
```

### Key rotation

```
cd $PGDATA
```

```
set -a
source /etc/pgee/pgee_service.env
pgee_key_manager -vault -vault-mount tde-cybertec -vault-path zwsbii/nonprod/hostname/data -rotate
```

The key version is stored in the ***tdekey.json*** file.


### Transparent Data Encryption verification

```
postgres=# show data_encryption;
data_encryption
-----------------
on
(1 row)
```