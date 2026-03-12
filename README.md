# nais/login

## Usage

```yaml
- uses: nais/login@v0
  with:
    team: myteam
```

## Configuration

The intention is for the Github organization administrator to create a secret with the workload identity provider url and a variable containing the Nais management project id.

## Dependency pinning

We use [ratchet](https://github.com/sethvargo/ratchet) to pin GitHub Actions to commit SHAs, with the source version constraint stored as an inline comment:

```yaml
uses: docker/login-action@b45d80f862d83dbcd57f89517bcf500b2ab88fb2 # ratchet:docker/login-action@v4
```

### Update action dependencies

1. Upgrade all GitHub Action references to the latest major and re-pin them:

```bash
docker run --rm -v "${PWD}:${PWD}" -w "${PWD}" ghcr.io/sethvargo/ratchet:latest upgrade action.yml README.md .github/workflows/*.yml
```

2. Verify that all references are pinned:

```bash
docker run --rm -v "${PWD}:${PWD}" -w "${PWD}" ghcr.io/sethvargo/ratchet:latest lint action.yml README.md .github/workflows/*.yml
```

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
