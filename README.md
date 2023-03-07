# Usage

```yaml
- uses: nais/login@v0
  with:
    project_id: ${{ vars.NAIS_MANAGEMENT_PROJECT_ID }}
    identity_provider: ${{ secrets.NAIS_WORKLOAD_IDENTITY_PROVIDER }}
    team: myteam
```

## Configuration

The intention is for the Github organization administrator to create a secret with the workload identity provider url and a variable containing the Nais management project id.
