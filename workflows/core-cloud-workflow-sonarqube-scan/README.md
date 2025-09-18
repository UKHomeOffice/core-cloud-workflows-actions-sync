# core-cloud-workflow-sonarqube-scan

## Overview
This repository contains reusable Github Actions workflow files for using Sonarqube scanner. Once the scan is complete, the workflow will push the results to our Sonarqube instance for analysis and, if executed via PR instead of on every commit push, will auto-comment the results on your respective PR.

The reusable workflow file exists here. This is for informational purposes only.
[sonarqube-scan.yaml](https://github.com/UKHomeOffice/core-cloud-workflow-sonarqube-scan/blob/main/.github/workflows/sonarqube-scan.yaml)

## Implementation

### Tokens
In Sonarqube, each project will have a project admin account associated with it. This is where the Sonar Scanner token will be created. Ask your project admin to generate a User Token on your tenant admin account.

Project admin steps:
- Login to Sonarqube.
- Click your user in the top right of the screen, then click "My Account".
- On the Security tab, create a name, select token type as "User Token", and set "No Expiration".
- Go to the Github repo and add the token in Settings -> Secrets and Variables -> Actions -> New Repository Secret.

Secret names to use:
SONAR_TOKEN (Contains the token you just created)
SONAR_HOST_URL (contains the Sonarqube host e.g. https://sonarqube.cc-platform-ops-tooling-test-1.np.core.homeoffice.gov.uk)

Add the following config into the following directory in your repository `.github/workflow/sonarqube-scan.yaml`, or build into your own workflow logic if more complex.

    name: Sonarqube Scanner
    
    on:
      workflow_call:
        secrets:
          sonar_token:
            required: true
          sonar_host_url:
            required: true
      push:
        branches:
          - main
      pull_request:
        branches:
          - main
    
    permissions:
      contents: read
      id-token: write
      actions: read
      security-events: write
    
    jobs:
      sonarqube-scanner:
        uses: UKHomeOffice/core-cloud-workflow-sonarqube-scan/.github/workflows/sonarqube-scan.yaml@1.0.0
        secrets:
          sonar_token: ${{ secrets.sonar_token }}
          sonar_host_url: ${{ secrets.sonar_host_url }}

# Notes
- If you wish to add your own `sonar-project.properties` file for further customisation of your Sonarqube project, this is supported by the workflow. Please add this to your repo's root directory. If you wish to use your own projectKey and name instead of the repo name, you can change this here. An example of this config would be
```
     sonar.projectKey=UKHomeOffice:james-test-sonarqube-name-override
     sonar.projectName=James Test Sonarqube Name Override
     sonar.projectVersion=1.0.0
     sonar.sources=.
     sonar.qualitygate.wait=false
     sonar.issues.fail=false
```
Github - When adding the Sonar feature to an existing repo, it would be best to push the Sonar feature on its own to your primary branch. This will highlight any existing code quality issues your primary branch currently has.
