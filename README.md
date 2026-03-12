# nais/login

## Usage

```yaml
- uses: nais/login@v0
  with:
    team: myteam
```

## Configuration

The intention is for the Github organization administrator to create a secret with the workload identity provider url and a variable containing the Nais management project id.

## Use case example: Trivy security scan in a reusable workflow

Below is a [reusable Github Actions workflow](https://docs.github.com/en/actions/sharing-automations/reusing-workflows). The `nais/login` Github Action is used to authentice with an artifact registry, which is necessary for the `aquasecurity/trivy-action` Github Action to be able to download a Docker image, so that the image can be scanned for security issues.
```
on:
  workflow_call:
    inputs:
      image:
        required: true
        type: string

jobs:
  trivy-security-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Authenticate to access Docker image
        uses: nais/login@v0
        with:
          team: <your team name here>

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@bceef37a45815d6c5cfea1861a414ec7dbc00242 # ratchet:aquasecurity/trivy-action@v0
        with:
          image-ref: '${{ inputs.image }}'
          format: 'sarif'
          output: 'trivy-results.sarif'
          severity: 'HIGH,CRITICAL'
          limit-severities-for-sarif: true

      - name: Upload Trivy results to GitHub Security
        uses: github/codeql-action/upload-sarif@0d579ffd059c29b07949a3cce3983f0780820c98 # ratchet:github/codeql-action/upload-sarif@v4
        with:
          sarif_file: 'trivy-results.sarif'
```
