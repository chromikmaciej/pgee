# Dump roles

```
pg_dumpall --roles-only > roles.sql
```

# Restore

```
psql -f roles.sql postgres
```

# Copy folders in a disabled database

```
cp -rap data data_file_bck
```

> cp - copy files/directories
> -r - recursive (copy directories and their contents)
>