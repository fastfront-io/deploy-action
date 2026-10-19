# FastFront Deploy

GitHub Action that deploys a static folder to [FastFront](https://www.fastfront.io).
It runs the FastFront CLI from the public image `ghcr.io/fastfront-io/cli`.

## Usage

1. Create an API key in FastFront.
2. Add it as an Actions secret named `FASTFRONT_API_KEY`
   (repository **Settings → Secrets and variables → Actions → New repository secret**).
3. Add a deploy step to your workflow:

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: read
    steps:
      - name: "Checkout code"
        uses: actions/checkout@v7

      # ... build your site into ./dist

      - name: "Deploy to FastFront"
        id: fastfront
        uses: fastfront-io/deploy-action@v0.0.1
        with:
          folder: './dist'
          # environment: 'production'   # optional, defaults to the branch name
          api_key: ${{ secrets.FASTFRONT_API_KEY }}

      - run: echo "Deployed to ${{ steps.fastfront.outputs.url }}"
```

| Input | Required | Description |
| --- | --- | --- |
| `folder` | yes | Folder to deploy |
| `api_key` | yes | FastFront project (`p_...`) or stage (`s_...`) API key |
| `environment` | no | Stage key, defaults to the slugified branch or tag name |
| `timeout` | no | Maximum wait time, default `10m` |

| Output | Description |
| --- | --- |
| `deployment_id` | ID of the deployment |
| `stage` | Stage key that was deployed to |
| `url` | `https://` URL of the stage's first hostname, if it has one |

## Limitations

- Runs only on Linux runners (it is a Docker container action).
- Secrets are not available to workflows triggered from forks, so deployments
  of pull requests from forks fail with a missing API key.
