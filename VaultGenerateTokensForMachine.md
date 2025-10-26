# Generate toekns for machine authentication with AppRole

## Scenario introduction

1. Enable the approle auth method
2. Create a policy and a role
3. Get the ***Role ID***
4. Get the ***Secret ID*** od the role
5. Deliver the ***Role ID***
6. Deliver the ***Secret ID***
7. Authenticate with Vault using the ***Rold Id*** and ***Secret ID***

### Varify your connectivity to the Vault cluster

```
vault status
```

### Step 1. Enable AppRole auth method

```
vault auth enable approle
```

### Step 2. Create a role with policy attached

```
vault policy write jenkins -<<EOF
# Read-only permission on secrets stored at 'secret/data/mysql/webapp'
path "secret/data/mysql/webapp" {
  capabilities = [ "read" ]
}
EOF

```