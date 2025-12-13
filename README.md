# Reusable GitHub Actions Workflows

A collection of reusable GitHub Actions workflows for Bun and Nix projects.

## Setup

1. Create a new repository (e.g., `github-actions`)
2. Copy the `workflows/` directory to `.github/workflows/` in the new repo
3. Reference these workflows from your other repositories

## Available Workflows

### `bun-ci.yml`

CI workflow for Bun projects with lint, typecheck, build, and test jobs.

**Usage:**

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  ci:
    uses: YOUR_ORG/github-actions/.github/workflows/bun-ci.yml@main
    with:
      run-lint: true
      run-typecheck: true
      run-build: true
      run-test: false  # Enable if you have tests
```

**Inputs:**

| Input | Description | Default |
|-------|-------------|---------|
| `bun-version` | Bun version to use | `latest` |
| `run-lint` | Run linting | `true` |
| `run-typecheck` | Run type checking | `true` |
| `run-build` | Run build | `true` |
| `run-test` | Run tests | `false` |
| `lint-command` | Lint command | `bun run lint` |
| `typecheck-command` | Typecheck command | `bun run tsc --noEmit` |
| `build-command` | Build command | `bun run build` |
| `test-command` | Test command | `bun test` |
| `working-directory` | Working directory | `.` |

---

### `nix-build.yml`

Nix build workflow with optional bun.nix sync check.

**Usage:**

```yaml
name: Nix Build

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  nix:
    uses: YOUR_ORG/github-actions/.github/workflows/nix-build.yml@main
    with:
      check-bun-nix-sync: true
```

**Inputs:**

| Input | Description | Default |
|-------|-------------|---------|
| `check-bun-nix-sync` | Check if bun.nix is in sync | `true` |
| `nix-path` | Nix path | `nixpkgs=channel:nixos-unstable` |
| `build-command` | Build command | `nix build` |
| `working-directory` | Working directory | `.` |

---

### `update-bun-deps.yml`

Automated Bun dependency updates via PR.

**Usage:**

```yaml
name: Update Bun Dependencies

on:
  schedule:
    - cron: "0 6 * * *"
  workflow_dispatch:

jobs:
  update:
    uses: YOUR_ORG/github-actions/.github/workflows/update-bun-deps.yml@main
    secrets:
      token: ${{ secrets.PAT }}
```

**Inputs:**

| Input | Description | Default |
|-------|-------------|---------|
| `bun-version` | Bun version | `latest` |
| `update-bun-nix` | Also update bun.nix | `true` |
| `branch-name` | PR branch name | `deps/bun-update` |
| `commit-message` | Commit message | `chore: update bun dependencies` |
| `pr-title` | PR title | `chore: update bun dependencies` |
| `pr-body` | PR body | _(see workflow)_ |
| `working-directory` | Working directory | `.` |

**Secrets:**

| Secret | Description | Required |
|--------|-------------|----------|
| `token` | GitHub PAT (to trigger CI on PRs) | Yes |

---

### `update-nix-flake.yml`

Automated Nix flake input updates via PR.

**Usage:**

```yaml
name: Update Nix Flake

on:
  schedule:
    - cron: "0 6 * * *"
  workflow_dispatch:

jobs:
  update:
    uses: YOUR_ORG/github-actions/.github/workflows/update-nix-flake.yml@main
    secrets:
      token: ${{ secrets.PAT }}
```

**Inputs:**

| Input | Description | Default |
|-------|-------------|---------|
| `branch-name` | PR branch name | `deps/nix-flake-update` |
| `commit-message` | Commit message | `chore: update nix flake inputs` |
| `pr-title` | PR title | `chore: update nix flake inputs` |
| `pr-body` | PR body | _(see workflow)_ |
| `working-directory` | Working directory | `.` |

**Secrets:**

| Secret | Description | Required |
|--------|-------------|----------|
| `token` | GitHub PAT (to trigger CI on PRs) | Yes |

## PAT Setup (for dependency update workflows)

To ensure CI runs on auto-created PRs, you need a Personal Access Token:

1. Go to GitHub Settings → Developer settings → Personal access tokens
2. Create a fine-grained token with:
   - Repository access: Select your repositories
   - Permissions: Contents (read/write), Pull requests (read/write)
3. Add the token as a repository secret named `PAT`

## Repository Structure

```
.github/
  workflows/
    bun-ci.yml
    nix-build.yml
    update-bun-deps.yml
    update-nix-flake.yml
README.md
```
