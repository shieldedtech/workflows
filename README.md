# Shielded Technologies reusable workflows

Reusable GitHub Actions workflows for label and lifecycle automation on issues and pull requests in [`shieldedtech`](https://github.com/shieldedtech) repositories.

Each workflow here is triggered with `workflow_call`. A repository uses one by adding a small caller workflow that listens for the event and calls the workflow in this repository.

## Workflows

| Workflow | What it does | Caller trigger | Caller permissions | Secrets |
| --- | --- | --- | --- | --- |
| `add-issue-to-project.yml` | Adds a new issue to a Projects v2 board with `Backlog` status. | `issues: [opened]` | `issues: read` | `PROJECT_TOKEN` |
| `ai-assisted-label.yml` | Applies `bot:ai-assisted` to a pull request whose commits or body carry an AI-tooling signature. | `pull_request` | `contents: read`, `pull-requests: write` | none |
| `apply-audience-default.yml` | Applies `audience:shielded` to a new issue that has no `audience:*` label. | `issues: [opened]` | `issues: write` | none |
| `apply-priority-from-form.yml` | Sets the organisation Priority issue field from the `### Priority` section of an issue form. | `issues: [opened, edited]` | `contents: read`, `issues: write` | `PROJECT_TOKEN` |
| `drop-untriaged-on-assign.yml` | Removes `status:untriaged` from an issue once it has an assignee. | `issues: [assigned, labeled]` | `issues: write` | none |
| `nudge-pr-without-issue-link.yml` | Comments once on a pull request that links no issue, asking for `Closes #<n>`. Never blocks or closes. | `pull_request: [opened, ready_for_review]` | `contents: read`, `pull-requests: write` | none |

Notes:

- The workflows never create labels. If the label is missing from the calling repository, the workflow logs that and skips.
- The pull request workflows use `pull_request`, not `pull_request_target`, so they do not run with a write token on pull requests from forks.
- `PROJECT_TOKEN` is needed because the default `GITHUB_TOKEN` cannot write to organisation projects or issue fields. The header comment of each workflow states the scope it needs.

### Inputs

| Workflow | Input | Default | Purpose |
| --- | --- | --- | --- |
| `add-issue-to-project.yml` | `project_number` | `50` | Projects v2 number of the target board. |
| `add-issue-to-project.yml` | `project_owner` | `shieldedtech` | Organisation that owns the board. |
| `apply-priority-from-form.yml` | `native_field_id` | `43453170` | Id of the organisation Priority issue field. |

## Usage

Add a caller workflow to the repository and pin the reference to a full commit SHA:

```yaml
name: Label AI-assisted PRs

on:
  pull_request:
    types: [opened, synchronize, reopened, ready_for_review]

permissions: {}

jobs:
  label:
    permissions:
      contents: read
      pull-requests: write
    uses: shieldedtech/workflows/.github/workflows/ai-assisted-label.yml@<commit-sha>  # pin to a commit SHA
```

A workflow that needs `PROJECT_TOKEN` takes it from the caller:

```yaml
jobs:
  add-to-project:
    permissions:
      issues: read
    uses: shieldedtech/workflows/.github/workflows/add-issue-to-project.yml@<commit-sha>  # pin to a commit SHA
    secrets:
      PROJECT_TOKEN: ${{ secrets.PROJECT_TOKEN }}
```

Reusable workflows without their own `permissions:` block inherit the caller's, so grant only what the table above lists.

## Pinning

Callers pin these workflows, and every third-party action, to a full commit SHA with the version in a trailing comment. Tags can be moved; commit SHAs cannot. Dependabot keeps the pins current.

## Contributing

See [`CONTRIBUTING.md`](CONTRIBUTING.md). Commits are signed and signed off for the DCO, and every pull request links an issue. Report vulnerabilities through the process in [`SECURITY.md`](SECURITY.md).

`scan.yaml` is this repository's own security scan and is not a reusable workflow.

## License

Apache-2.0. See [`LICENSE`](LICENSE).
