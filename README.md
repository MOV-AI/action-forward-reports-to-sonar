# Collect different reports and run Sonar

With this GitHub Action you can collect issues from multiple sources and forward them to Sonar.

## Requirements

To run an analysis on your code, you first need to set up your project on Sonar. Your Sonar instance must be accessible from GitHub, and you will need an access token to run the analysis (more information below.

## Reports interface

The action rellies on the caller to place reports in specific folders:
* `./generic-reports` - Should contain generic Sonar reports (as specified [here](https://docs.sonarqube.org/latest/analysis/generic-issue/)).

## Usage

This action does not require the file `sonar-project.properties`, instead the parameters are provided via argument.

The workflow YAML file will usually look something like this:

```yaml
on:
  push:
    branches:
      - main

name: Main Workflow
jobs:
  sonarqube:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
      with:
        # Disabling shallow clone is recommended for improving relevancy of reporting
        fetch-depth: 0

    - name: Generate reports
      run: |
        mkdir -p ./generic-reports
        echo '{"issues": []}' ./generic-reports/mobtest-issues.json

    - name: Collect reports and run Sonar
      uses: MOV-AI/action-forward-reports-to-sonar@main
      with:
        # required for public repositories
        sonarcloud_token: ${{ secrets.sonar_public_token }}
        # requires for private repositories
        sonarqube_token: ${{ secrets.sonar_token }}
        sonarqube_host_url: <YOUR-SONARQUBE-URL> # without trailing /
```

### Inputs

- **Required for public repositories**
  - `sonarqube_token` - Token used to authenticate access to SonarCloud. You can generate a token on your [Security page in SonarCloud](https://sonarcloud.io/account/security/).

- **Required for private repositories**
  - `sonarqube_token` - Token used to authenticate access to SonarQube. You can read more about security tokens [here](https://docs.sonarqube.org/latest/user-guide/user-token/).
  - `sonarqube_host_url` - Scanner URL where SonarQube is hosted, **without trailling /**.
