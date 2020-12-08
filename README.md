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

## AWS Permissions

Above workflows require following minimum set of permissions:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GetAuthorizationToken",
      "Effect": "Allow",
      "Action": ["ecr:GetAuthorizationToken"],
      "Resource": "*"
    },
    {
      "Sid": "AllowPush",
      "Effect": "Allow",
      "Action": [
        "ecr:GetDownloadUrlForLayer",
        "ecr:BatchGetImage",
        "ecr:BatchCheckLayerAvailability",
        "ecr:PutImage",
        "ecr:InitiateLayerUpload",
        "ecr:UploadLayerPart",
        "ecr:CompleteLayerUpload"
      ],
      "Resource": "arn:aws:ecr:us-east-1:123456789012:repository/my-repo"
    },
    {
      "Sid": "RegisterTaskDefinition",
      "Effect": "Allow",
      "Action": [
        "ecs:RegisterTaskDefinition",
        "ecs:ListTaskDefinitions",
        "ecs:DescribeTaskDefinition"
      ],
      "Resource": "*"
    },
    {
      "Sid": "PassRolesInTaskDefinition",
      "Effect": "Allow",
      "Action": ["iam:PassRole"],
      "Resource": [
        "arn:aws:iam::<aws_account_id>:role/<task_definition_task_role_name>",
        "arn:aws:iam::<aws_account_id>:role/<task_definition_task_execution_role_name>"
      ]
    },
    {
      "Sid": "DeployService",
      "Effect": "Allow",
      "Action": ["ecs:UpdateService", "ecs:DescribeServices"],
      "Resource": [
        "arn:aws:ecs:<region>:<aws_account_id>:service/<cluster_name>/<service_name>"
      ]
    }
  ]
}
```
