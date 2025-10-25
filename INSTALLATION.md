# Installation guide Red Hat family


## Step 1: Reach the RPM repository

The RPM repositories are located here:

>https://repository.cybertec.at

## Step 2: Disable the standard built-in modules

```
dnf module disable -y postgresql
```
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


##Step 4: Install PGEE from the repository

```
yum install -y postgresql17-ee-server
```

## Step 5 Create sample keys

```
su postgres
version=16
PATH=/usr/pgsql-$version/bin:$PATH
KEY=$(dd if=/dev/random bs=1k count=1 | md5sum - | cut -d ' ' -f 1)
```
  
> [!CAUTION]
> Save securly vaule **$KEY** variable




### A third-level


** Bold text **

*Italic*

~~This was mistaken text~~

**This text is _extremaly_ important**

***All bold and italic***

This is a <sub>subscript</sub> text

<ins>Underline</ins>

Text that is not a quote
> Text that is a quote
> Text that is a quote


1. First list item
    - First nested list item
        - Second nested list item

> [!NOTE]
> Useful information that users should know, even when skimming content.

> [!TIP]
> Helpful advice for doing things better or more easily.

> [!IMPORTANT]
> Key information users need to know to achieve their goal.

> [!WARNING]
> Urgent info that needs immediate user attention to avoid problems.

> [!CAUTION]
> Advises about risks or negative outcomes of certain actions.

```
$ dnf upudat e-y
Polecenie 'dnf' nie zostało znalezione, ale można je zainstalować za pomocą:
apt install dnf
Proszę zapytać swojego administratora.
```
