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

# Creating a new, empty, encrypted database

> [!NOTE]
> When we create a new DATA directory, we generate a new TDE key, which is stored in an encrypted JSON file. The encrypted key is protected using a key stored in Vault. When we rotate passwords, we rotate the key in Vault, not the TDE key.