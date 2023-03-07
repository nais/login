# Usage

```yaml
- uses: nais/login@v0
  with:
    project_id: ${{ inputs.project_id }}
    identity_provider: ${{ inputs.identity_provider }}
    team: ${{ inputs.team }}
```

## Configuration

The intention is for the Github organization administrator to create a secret with the workload identity provider url and a variable containing the Nais management project id.
