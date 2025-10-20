# Core Cloud Workflows Actions Sync
GHES instances cannot access reusable workflows stored in github.com.
This is a consumable reusable workflow that synchronises your repo with a single or multiple GHES instances.

## Prerequisites

1: Ensure the workflows-actions-sync Github app exists in the GHES instance you're syncing with.
2: If not, create a new Github app with read-write Repository permissions on Administration, Contents and Workflows, note the app id, generate a private key, store both in AWS Secrets Manager in the GHES instance's AWS account and install the app under the correct organization.

## Setup

1: Go to your repo's Settings > Environments.
2: Add the name of the environment where the GHES instance resides.
3: Add secrets APP_ID and PRIVATE_KEY. Values can be found in Secrets Manager in the AWS account that the GHES instance resides in.
4: Add the following caller workflow to your repo and edit accordingly.

```
name: Sync with GHES Instance

on:
  push:
    tags:
      - '*'
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: write

jobs:
  sync-<ENV_NAME1>:
    name: Sync to <ENV_NAME1> GHES instance
    uses: UKHomeOffice/core-cloud-workflows-actions-sync/.github/workflows/sync_hub.yaml@1.0.0
    with:
      ghes-instance-url: 'https://<GHES-HOSTNAME1>'
      ghes-owner: '<GHES-OWNER1>'
      environment: '<ENV-NAME1>'
    secrets: inherit
  sync-<ENV_NAME2>:
    name: Sync to <ENV_NAME2> GHES instance
    uses: UKHomeOffice/core-cloud-workflows-actions-sync/.github/workflows/sync_hub.yaml@1.0.0
    with:
      ghes-instance-url: 'https://<GHES-HOSTNAME2>'
      ghes-owner: '<GHES-OWNER2>'
      environment: '<ENV-NAME2>'
    secrets: inherit
```
5: Push a new tag or merge to main branch to start the process.
