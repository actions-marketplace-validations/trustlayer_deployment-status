# Deployment Status

GitHub Action that allows to update the deployment Status in a PR.

![Deployment status](https://user-images.githubusercontent.com/6388707/141519226-0fb89759-6330-47d9-8937-41fe28c3a637.png)

## Parameters

### Inputs

- `state`: Deployment state. (default: pending)
- `description`: Descriptive message about the deployment state.
- `log-url`: Log url location.
- `deployment-id`: Optional deployment ID to provide, if this action was triggered outside of a `deployment` event.
- `token`: Github repository token.
- `ref`: Pull request branch ref
- `environment`: Name for the target deployment environment, which can be changed when setting a deploy status.
- `environment-url`: URL for accessing your environment.

## Example

```yaml
# .github/workflows/deploy.yml
name: Deploy

jobs:
  deployment:
    runs-on: "ubuntu-latest"

    steps:
      - uses: actions/checkout@v1
      - name: "Use node 12.x"
        uses: actions/setup-node@v1
        with:
          node-version: 12.x

      - uses: chrnorm/deployment-action@releases/v1
        name: Create GitHub deployment
        id: deployment
        with:
          token: ${{ secrets.PAT }}
          target_url: https://cuzzo.it
          ref: ${{github.head_ref }}
          environment: preview

      - name: "Deployment status update: pending"
        uses: "trustlayer/deployment-status@v1.2.3"
        with:
          state: "pending"
          token: "${{ github.token }}"
          deployment-id: ${{ steps.deployment.outputs.deployment_id }}
          ref: ${{github.head_ref }}

      ##################
      #  Your pipeline here
      ##################

      - name: "Deployment status update: success"
        if: success()
        uses: "trustlayer/deployment-status@v1.2.3"
        with:
          state: "success"
          token: "${{ github.token }}"
          deployment-id: ${{ steps.deployment.outputs.deployment_id }}
          ref: ${{github.head_ref }}

      - name: "Deployment status update: failure"
        if: failure()
        uses: "trustlayer/deployment-status@v1.2.3"
        with:
          state: "failure"
          token: "${{ github.token }}"
          deployment-id: ${{ steps.deployment.outputs.deployment_id }}
          ref: ${{github.head_ref }}
```

## How to create a new version

Before pushing your changes, build index.js code with @vercel/ncc:

`npx ncc build index.js --license licenses.txt`
