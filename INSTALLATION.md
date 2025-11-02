# Installation guide Red Hat family


## Step 1: Reach the RPM repository

The RPM repositories are located here:

>https://repository.cybertec.at

## Step 2: Disable the standard built-in modules

```
dnf module disable -y postgresql
```

>[!IMPORTANT]
>Run this command as the **root** user

## Step 3: Setting up the repository

### Public PGEE Demo Version Repository

```
version=17 # available: 13 14 15 16 17

# RedHat, CentOS, Rocky Linux, Almalinux
sudo tee /etc/yum.repos.d/cybertec-pg$version.repo <<EOF
[cybertec_pg$version]
name=CYBERTEC PostgreSQL $version repository for RHEL/CentOS \$releasever - \$basearch
baseurl=https://repository.cybertec.at/public/$version/redhat/\$releasever/\$basearch
gpgkey=https://repository.cybertec.at/assets/cybertec-rpm.asc
enabled=1
[cybertec_common]
name=CYBERTEC common repository for RHEL/CentOS \$releasever - \$basearch
baseurl=https://repository.cybertec.at/public/common/redhat/\$releasever/\$basearch
gpgkey=https://repository.cybertec.at/assets/cybertec-rpm.asc
enabled=1
EOF
```

### Full PGEE Version Repository

```
version=17 # available: 13 14 15 16 17
username="YOUR_LOGIN"
password="YOUR_PASSWORD"

# RedHat, CentOS, Rocky Linux, Almalinux
sudo tee /etc/yum.repos.d/cybertec-pg$version.repo <<EOF
[cybertec_pg$version]
name=CYBERTEC PostgreSQL $version repository for RHEL/CentOS \$releasever - \$basearch
baseurl=https://repository.cybertec.at/pgee/$version/redhat/\$releasever/\$basearch
gpgkey=https://repository.cybertec.at/assets/cybertec-rpm.asc
username=$username
password=$password
enabled=1
[cybertec_common]
name=CYBERTEC common repository RHEL/CentOS \$releasever - \$basearch
baseurl=https://repository.cybertec.at/pgee/common/redhat/\$releasever/\$basearch
gpgkey=https://repository.cybertec.at/assets/cybertec-rpm.asc
username=$username
password=$password
enabled=1
EOF
```


## Step 4: Install PGEE from the repository

```
dnf install -y postgresql17-ee-server postgresql17-ee postgresql17-ee-contrib
```

### Install pgee_key_manager from the repository

```
dnf install -y pgee_key_manager
```

## Step 5 Create sample keys

Switch to user ***postgres***


```
version=17
```
```
PATH=/usr/pgsql-$version/bin:$PATH
```
```
KEY=$(dd if=/dev/random bs=1k count=1 | md5sum - | cut -d ' ' -f 1)
```
  
> [!CAUTION]
> Save the value of **$KEY** securely.


## Step 6 Create the database instance

For encrypted installs, you need to specify to PGEE how to get the key; normal installs don’t require this.
Use the **-K** option to provide a script to the server that supplies the key.

```
initdb -D /var/lib/pgsql/$version/data -k -K "echo $KEY"
```
>[!IMPORTANT]
>Set the **$version** variable to the desired value.

## Step 7 Starting your PGEE instance

Now we’ll start the server. There are two methods:

### Starting PGEE manually

```
pg_ctl -D /var/lib/pgsql/$version/data start
```
>[!IMPORTANT]
>Set the **$version** variable to the desired value.
>Run this command as the **postgres** user

### Starting PGEE using systemd

```
sudo systemctl enable postgresql-17
```
```
sudo systemctl start postgresql-17
```

## Step 8 Verifying encryption

```
$ psql

postgres=# SHOW data_encryption;
data_encryption
-----------------
on
(1 row)
```

## Step 9 Upgrading from demo packages to the full PGEE version

Replace the demo repo with the full-version repo described earlier, provide your username/password, and run **dpkg upgrade**. The upgrade installs the full PGEE packages (demo versions are numbered slightly lower to permit the upgrade). Existing clusters keep their data and will run PGEE going forward.