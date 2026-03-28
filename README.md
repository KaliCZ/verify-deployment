# verify-deployment

A reusable GitHub Action that polls a health endpoint until it returns a healthy response. Useful for verifying deployments are live before continuing a CI/CD pipeline.

## Usage

```yaml
- uses: KaliCZ/verify-deployment@v1
  with:
    health-url: https://api.example.com/health
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `health-url` | Yes | — | The full URL of the health endpoint to poll |
| `max-attempts` | No | `20` | Maximum number of attempts before failing |
| `retry-interval` | No | `5` | Seconds to wait between retries |

## Example

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy
        run: ./deploy.sh

      - name: Verify deployment
        uses: KaliCZ/verify-deployment@v1
        with:
          health-url: https://api.example.com/health
          max-attempts: 30
          retry-interval: 10
```

## How it works

The action uses `curl -sf` to poll the given URL in a loop. If the endpoint returns a 2xx response, the check passes. If all attempts are exhausted without a healthy response, the step fails with an error annotation.

## License

MIT
