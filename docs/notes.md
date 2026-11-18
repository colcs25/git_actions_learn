# GitHub Actions

## What is GitHub Actions?
GitHub Actions are packaged scripts to automate tasks in a software-development workflow in GitHub.
   - Can be configured to trigger complex workflows
   - Triggers can happen on commits/push, cron or manually.
   - They are scripts that adhere to a yml data format.

GitHub Marketplace allows you to discover and purchase tools that extend your workflow.
There are open source projects for GitHub Actions.
Write your own GitHub Actions from scratch.

## Types of GitHub actions
Three types: container actions, JavaScript actions, and composite actions.

### 1. Container actions: 
The environment is part of the action's code. Will only run in Linux env hosted by GitHub. Supports many languages.

### 2. JavaScript actions:
Doesn't contain environment in code. User specifies env to execute the action. Can run on VM in cloud or on-prem. Support Linux, macOS, Windows env.

### 3. Composite actions:
Combine multiple workflows steps into one action. Eg: Bundle multiple run commands into an action, then workflow executes the commands as a single step using actions.

## What is a GitHub Actions workflow?
It's the process that you setup in the repo to automate SDLC tasks, including GitHub Actions. With a workflow, you can build, test, package, release, and deploy any project on GitHub.

Workflow can have one or more jobs. The jobs contains one or more steps. Triggered upon a event.

Workflows is attached to a repository.

Jobs require a defined runner. contains one or more steps. Run in parallel or sequential. Can be conditional

Steps execute a shell script or action. use custom or third party actions. Executed in order. Can be conditional

## Events (Workflow Triggers)

Events are defined under `on:` and decide **when** a workflow runs.

### Repository-related events
| Event | Triggers when… |
|---|---|
| `push` | Commits are pushed to a branch, or a tag is pushed |
| `pull_request` | Activity on a PR (opened, updated, reopened, etc.) |
| `pull_request_target` | Like `pull_request`, but runs in the context of the **base** branch with secrets access. Risky with forks |
| `create` | A branch or tag is created |
| `delete` | A branch or tag is deleted |
| `fork` | Someone forks the repo |
| `issues` | Activity on an issue (opened, edited, closed, labeled, …) |
| `issue_comment` | A comment on an issue **or** a PR is created, edited or deleted |
| `release` | A release is published, created, edited, … |
| `watch` | Someone stars the repo |
| `discussion` | Activity on a GitHub Discussion |

> There is no `commit` event. Pushing commits triggers `push`.

### Other events
| Event | Triggers when… |
|---|---|
| `workflow_dispatch` | Manually, via the "Run workflow" button, the API or the CLI. Can take `inputs` |
| `workflow_call` | Another workflow calls this one (makes it a **reusable workflow**) |
| `schedule` | On a cron schedule (UTC). Shortest interval is every 5 min. Runs only on the default branch |
| `repository_dispatch` | An external system sends a webhook via the GitHub API |
| `workflow_run` | Another workflow completes or is requested (chains workflows) |

### Multiple events
```yaml
on: [push, workflow_dispatch]          # list form
```

### Event filters and activity types

**Activity types** control *which kind* of activity on an event triggers the workflow (`types:`):
```yaml
on:
  pull_request:
    types: [opened, edited, synchronize]   # synchronize = new commits pushed to the PR
  issues:
    types: [opened, labeled]
```
- If `types` is omitted, a **default set** is used. For `pull_request` that's `opened`, `synchronize` and `reopened`.

**Filters** control *where* the event happened (branches, tags, paths):
```yaml
on:
  push:
    branches:
      - main
      - 'dev-*'          # glob: dev-new, dev-feature
      - 'feat/**'        # ** matches slashes: feat/a/b
    paths-ignore:
      - '.github/workflows/*'   # don't run when only workflow files change
  pull_request:
    branches: [main]     # for PRs, this is the TARGET (base) branch
```

| Filter | Include | Exclude |
|---|---|---|
| Branches | `branches` | `branches-ignore` |
| Tags | `tags` | `tags-ignore` |
| File paths | `paths` | `paths-ignore` |

- You can't use `branches` and `branches-ignore` together for the same event (same for `paths`). To include and exclude at once, use `!` negation in `branches`/`paths`, e.g. `'!docs/**'`.
- If `branches` and `paths` are both set, **both** must match.

### Exam gotchas
- `pull_request` from a **fork**: no access to secrets, and `GITHUB_TOKEN` is read-only.
- Workflows on `pull_request` from forks may need **maintainer approval** before they run.
- Add `[skip ci]` (or `[ci skip]`, `[no ci]`, `[skip actions]`, `[actions skip]`) to a commit message to skip `push`/`pull_request` workflows.
- `schedule` and `workflow_dispatch` (button) only work from the **default branch**.

### Skipping workflows

To stop a commit from triggering workflows, add one of these to the **commit message** (brackets included):

- `[skip ci]`
- `[ci skip]`
- `[no ci]`
- `[skip actions]`
- `[actions skip]`

```bash
git commit -m "Update README [skip ci]"
```

**Alternative: a commit trailer.** End the commit message with two empty lines, then:
```
skip-checks: true
```

**Rules**
- Works only for `push` and `pull_request` triggered workflows. Other events (`workflow_dispatch`, `schedule`, …) still run.
- For a pull request, the keyword must be in the **HEAD commit** (the latest commit on the PR branch).
- The **whole workflow** is skipped, not individual jobs.
- Watch out for **required status checks**: if branch protection requires a check from a skipped workflow, the check stays "pending" and the PR **can't be merged** until a new commit triggers the workflow.

**Other ways to avoid unnecessary runs**
- `paths-ignore` / `branches-ignore` filters under `on:` (e.g. skip when only `docs/**` changes)
- `if:` conditions on jobs or steps for finer control:
  ```yaml
  jobs:
    deploy:
      if: github.ref == 'refs/heads/main'
  ```

## Job Artifacts
