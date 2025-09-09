# Dependabot Auto Manage Action

Automatically approves and merges Dependabot Pull Requests for production dependencies.

## Features

- ✅ Automatic approval of Dependabot PRs
- ✅ Automatic merge with configurable method (squash, merge, rebase)
- ✅ Filter by dependency type (production, development, all)
- ✅ Add labels to approved PRs
- ✅ Support for auto-merge (requires branch protection rules)
- ✅ Safe PR author verification

## Usage

### Basic usage

```yaml
name: Dependabot Auto Manage
on: pull_request

permissions:
  contents: write
  pull-requests: write

jobs:
  dependabot:
    runs-on: ubuntu-latest
    if: github.event.pull_request.user.login == 'dependabot[bot]' || github.event.pull_request.user.login == 'app/dependabot'
    steps:
      - name: Dependabot auto-manage
        uses: ad/dependabot-auto-approve@v1
```

### Advanced configuration

```yaml
name: Dependabot Auto Manage
on: pull_request

permissions:
  contents: write
  pull-requests: write

jobs:
  dependabot:
    runs-on: ubuntu-latest
    if: github.event.pull_request.user.login == 'dependabot[bot]' || github.event.pull_request.user.login == 'app/dependabot'
    steps:
      - name: Dependabot auto-manage
        uses: ad/dependabot-auto-approve@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          dependency-type: 'direct:production'  # or 'direct:development', 'all'
          merge-method: 'squash'                # or 'merge', 'rebase'
          auto-merge: 'false'                   # 'true' for auto-merge (requires branch protection)
          add-label: 'dependabot-approved'      # label for approved PRs
```

## Input parameters

| Parameter | Description | Required | Default |
|-----------|-------------|----------|---------|
| `github-token` | GitHub token for API calls | No | `${{ github.token }}` |
| `dependency-type` | Type of dependencies to auto-merge | No | `direct:production` |
| `merge-method` | Merge method | No | `squash` |
| `auto-merge` | Enable auto-merge | No | `false` |
| `add-label` | Label for approved PRs | No | `dependabot-approved` |

### Dependency types

- `direct:production` - production dependencies only
- `direct:development` - development dependencies only
- `all` - all dependencies

### Merge methods

- `squash` - squash commits into one
- `merge` - regular merge
- `rebase` - rebase and merge

## Requirements

### Permissions

The workflow must have the following permissions:

```yaml
permissions:
  contents: write
  pull-requests: write
```

### Auto-merge

To use `auto-merge: 'true'` you need to:

1. Configure branch protection rules in Settings → Branches
2. Enable "Allow auto-merge" for the repository
3. Set up required status checks

## Security

- ✅ Verifies that PR is created by Dependabot
- ✅ Supports both `dependabot[bot]` and `app/dependabot` formats
- ✅ Filters by dependency type
- ✅ Uses official `dependabot/fetch-metadata` action

## Workflow examples

### Production dependencies only

```yaml
name: Dependabot Auto Manage
on: pull_request

permissions:
  contents: write
  pull-requests: write

jobs:
  dependabot:
    runs-on: ubuntu-latest
    if: github.event.pull_request.user.login == 'dependabot[bot]' || github.event.pull_request.user.login == 'app/dependabot'
    steps:
      - uses: ad/dependabot-auto-approve@v1
        with:
          dependency-type: 'direct:production'
          merge-method: 'squash'
```

### All dependencies with auto-merge

```yaml
name: Dependabot Auto Manage
on: pull_request

permissions:
  contents: write
  pull-requests: write

jobs:
  dependabot:
    runs-on: ubuntu-latest
    if: github.event.pull_request.user.login == 'dependabot[bot]' || github.event.pull_request.user.login == 'app/dependabot'
    steps:
      - uses: ad/dependabot-auto-approve@v1
        with:
          dependency-type: 'all'
          auto-merge: 'true'
          add-label: 'auto-merged'
```

## License

MIT License
