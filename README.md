# Terraform SDK for Workshop

This SDK provides the Terraform CLI for provisioning and managing infrastructure
as code inside a workshop. The binary is fetched from the official HashiCorp
release archive at build time and checksum-verified against the published
`SHA256SUMS`. The provider plugin cache and CLI credentials are persisted on the
host across workshop updates, so re-initialising a working directory does not
re-download providers.

---

## Reference workshop

A minimal workshop:

```yaml
# workshop.yaml
name: terraform-example
base: ubuntu@24.04
sdks:
  - name: terraform
    channel: latest/stable

actions:
  plan: |
    terraform -chdir=infra plan
```

The reference demonstrates the common case: Terraform on `PATH` for every login
shell, with `TF_PLUGIN_CACHE_DIR` already pointed at persisted storage.

---

## Using the SDK

### Prerequisites, project layout

1. No prerequisite SDKs are required. Pair it with the `azure` SDK if your
   configuration uses the `azurerm` provider and authenticates via `az login`.
2. No specific project layout is needed; point `-chdir` at wherever your
   `.tf` files live.
3. On launch the SDK puts `terraform` on `PATH`, registers bash completion, and
   creates the provider plugin cache directory.

### Running Terraform

```bash
workshop shell
terraform -chdir=infra init
terraform -chdir=infra plan
```

The first `init` populates the plugin cache. Subsequent workshop updates reuse
it rather than re-downloading providers.

### Verify from the command line

```bash
workshop shell
terraform version
echo "$TF_PLUGIN_CACHE_DIR"
```

---

## Plugs (resources this SDK consumes)

### `terraform-data`

- Interface: `mount`
- Workshop target: `/home/workshop/.terraform.d`
- Mode: `0o700`
- Purpose: Persists the provider plugin cache (`plugin-cache/`) and CLI
  credentials (`credentials.tfrc.json`) across workshop updates. Mode `0o700`
  because credentials live here.

## Slots (resources this SDK provides)

This SDK doesn't define any slots.

---

## Documentation and guidance

- [Terraform documentation](https://developer.hashicorp.com/terraform/docs)
- [Provider plugin caching](https://developer.hashicorp.com/terraform/cli/config/config-file#provider-plugin-cache)
- [Workshop documentation](https://canonical-workshop.readthedocs-hosted.com/latest/)

---

## Community and support

- Terraform community:
  [HashiCorp Discuss](https://discuss.hashicorp.com/c/terraform-core/27)
- Workshop forum:
  [Workshop Discourse](https://discourse.canonical.com/c/engineering/workshops/34)

---

## License and copyright

Copyright 2026 Nikolaos Papagrigoriou.

This repository's wrapper code, configuration (`sdkcraft.yaml`), integration
tests, and hooks are licensed under the [MIT License](./LICENSE).

The Terraform CLI itself is licensed under the
[Business Source License 1.1](https://github.com/hashicorp/terraform/blob/main/LICENSE).
This SDK downloads the official upstream binary at build time and does not
redistribute it.
