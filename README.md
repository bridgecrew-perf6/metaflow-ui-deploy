## Available workflows

- `deploy-ui`
  - Build and deploy `Netflix/metaflow-ui`
- `deploy-ui-service`
  - Build and deploy `Netflix/metaflow-service` (UI service only)

## Required secrets

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `ACCESS_TOKEN`
  - Personal access token for private `Netflix/metaflow-ui` repository

## Dispatch example

Following cURL command triggers a workflow that deploys UI service `Netflix/metaflow-service` from branch `ui`:

```bash
WORKFLOW="deploy-ui-service"
TOKEN="github-personal-access-token"

curl -X POST https://api.github.com/repos/savingoyal/metaflow-ui-deploy/actions/workflows/${WORKFLOW}.yml/dispatches \
    -H "Accept: application/vnd.github.v3+json" \
    -H "Authorization: token ${TOKEN}" \
    -d '{"ref":"master","inputs":{"version":"ui"}}'


# ref:              workflow version            (master)
# inputs.version:   target git ref to deploy    (ui)

```
