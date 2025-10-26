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
>Key             Value
>---             -----
>Seal Type       shamir
>Initialized     true
>Sealed          false
>Total Shares    5
>Threshold       3
>Version         1.20.3
>Build Date      2025-08-27T10:53:27Z
>Storage Type    file
>Cluster Name    vault.mc78.local
>Cluster ID      f55105a2-8db4-86af-5015-a096fb51aeb4
>HA Enabled      false