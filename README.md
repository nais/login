# Usage

```yaml
- uses: nais/login@v0
  with:
    config: ${{ vars.DOCKER_PUSH_CONFIG }} # required
```

## Configuration

The intention is for the organization admin to create a single secret containing required information.

```json
{
  "gar_service_account": "<service account email>",
  "gar_registry_url": "<registry url without scheme>",
  "workload_identity_provider": "<workload identity provider>"
}
```
