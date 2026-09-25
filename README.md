# dagsec GitHub Action

Checks every pull request for leaked credentials and risky npm, PyPI and crates.io dependencies, comments the report on the pull request, and fails the check when something needs attention.

## Setup

1. Sign in at [app.dagsec.net](https://app.dagsec.net) with GitHub and create an API key under **API keys**.
2. In your repository, add it as a secret: **Settings → Secrets and variables → Actions → New repository secret**, name `DAGSEC_API_KEY`.
3. Add `.github/workflows/dagsec.yml`:

```yaml
name: dagsec
on: pull_request
permissions:
  contents: read
  pull-requests: write
jobs:
  dagsec:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0 # full history is needed for secret scanning
      - uses: hasanerman/dagsec-action@v1
        with:
          api-key: ${{ secrets.DAGSEC_API_KEY }}
```

## Inputs

| Input | Default | |
|---|---|---|
| `api-key` | required | Your dagsec API key. Always pass it from a secret. |
| `fail-under` | `40` | Fail when a dependency's health score is below this. |
| `path` | `.` | Directory to scan. |
| `comment` | `true` | Post the report as a pull request comment. |

## Plans

The Free plan covers public repositories and 100 runs a month. Private repositories and higher limits need the Pro or Team plan; see [dagsec.net](https://dagsec.net).

## How it works

The scanner runs entirely on your GitHub runner; your code is never uploaded. The action downloads the dagsec scanner for your account, scans the commits your pull request adds and the dependencies it declares, and reports back on the pull request. Runs on Linux x64 runners.

Pull requests opened from forks don't receive repository secrets, so dagsec is skipped with an error on those.

---

© 2026 dagsec. All rights reserved. This repository contains only the Action definition; the dagsec scanner is proprietary software.
