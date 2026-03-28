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
| `expected-content` | No | — | A string that must appear in the response body (e.g., a commit SHA). When omitted, only HTTP 200 is checked. |
| `max-attempts` | No | `20` | Maximum number of attempts before failing |
| `retry-interval` | No | `5` | Seconds to wait between retries |

## Examples

### Basic health check

```yaml
- name: Verify deployment
  uses: KaliCZ/verify-deployment@v1
  with:
    health-url: https://api.example.com/health
```

### Verify commit hash

Use `expected-content` with `github.sha` to ensure the newly deployed version is actually live — not a cached or stale instance. This is useful during slot swaps or rolling deployments where the health endpoint may return 200 before the new code is serving traffic.

```yaml
- name: Verify deployment
  uses: KaliCZ/verify-deployment@v1
  with:
    health-url: https://api.example.com/health
    expected-content: ${{ github.sha }}
```

### Custom retry settings

```yaml
- name: Verify deployment
  uses: KaliCZ/verify-deployment@v1
  with:
    health-url: https://api.example.com/health
    max-attempts: 30
    retry-interval: 10
```

## How it works

The action uses `curl -sf` to poll the given URL in a loop. If the endpoint returns a 2xx response (and the response body contains `expected-content`, if provided), the check passes. If all attempts are exhausted without a healthy response, the step fails with an error annotation.

## License

MIT
