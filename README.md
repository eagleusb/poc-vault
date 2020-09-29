# poc-vault

## Traffic

| Source        | Destination   | port | protocol | Direction     | Purpose                                       |
|---------------|---------------|------|----------|---------------|-----------------------------------------------|
| Vault clients | Vault servers | 8200 | tcp      | incoming      | Vault API                                     |
| Vault servers | Vault servers | 8201 | tcp      | bidirectional | Vault replication traffic, request forwarding |
| Vault servers | Vault servers | 8201 | tcp      | bidirectional | Raft gossip traffic                           |

## Sizing

| Size  | CPU      | Memory       | Disk   | Typical Cloud Instance Types           |
|-------|----------|--------------|--------|----------------------------------------|
| Small | 2 core   | 8-16 GB RAM  | 100 GB | AWS: m5.large, m5.xlarge               |
|       |          |              |        | Azure: Standard_D2_v3, Standard_D4_v3  |
|       |          |              |        | GCE: n2-standard-2, n2-standard-4      |
| Large | 4-8 core | 32-64 GB RAM | 200 GB | AWS: m5.2xlarge, m5.4xlarge            |
|       |          |              |        | Azure: Standard_D8_v3, Standard_D16_v3 |
|       |          |              |        | GCE: n2-standard-8, n2-standard-16     |

## Raft Qorum Table

| Servers | Quorum Size | Failure Tolerance |
|---------|-------------|-------------------|
| 2       | 2           | 0                 |
| 3       | 2           | 1                 |
| 4       | 3           | 1                 |
| 5       | 3           | 2                 |
| 6       | 4           | 2                 |
| 7       | 4           | 3                 |

## Configuration

See [https://www.vaultproject.io/docs/configuration](https://www.vaultproject.io/docs/configuration)

> Configuration File

```terraform
disable_mlock = true
ui            = true

listener "tcp" {
  address     = "127.0.0.1:8200"
  tls_disable = "true"
}

# see https://www.vaultproject.io/docs/configuration/storage
storage "file" {
  path = "/tmp/vault-data"
}
storage "consul" {
  address = "127.0.0.1:8500"
  path    = "vault"
}
```

> Environment Variable

- VAULT_API_ADDR
- VAULT_CLUSTER_ADDR
- HTTP_PROXY
- HTTPS_PROXY
- NO_PROXY
