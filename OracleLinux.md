# Set a static address

```
nmcli device show
```

```
# nmcli connection modify ens33 ipv4.method manual \
ipv4.address 192.168.1.10/24 \ 
ipv4.gateway 192.168.1.1
ipv4.dns 1.1.1.1

```
```
# nmcli device reapply ens33

```