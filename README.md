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
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: '${{ inputs.image }}'
          format: 'sarif'
          output: 'trivy-results.sarif'
          severity: 'HIGH,CRITICAL'
          limit-severities-for-sarif: true

      - name: Upload Trivy results to GitHub Security
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: 'trivy-results.sarif'
```
