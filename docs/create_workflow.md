### What is Workflows and how to create one

A **workflow** is an automated process defined in a YAML file. It must live in
`.github/workflows/` in the repo (e.g. `.github/workflows/first-workflow.yml`),
or GitHub won't detect it. A repo can have many workflows.

**Hierarchy:** Workflow → Jobs → Steps
- **Workflow:** triggered by one or more events (`on:`)
- **Job:** a set of steps that runs on one runner (`runs-on:`). Jobs run in **parallel** by default; use `needs:` to make them sequential.
- **Step:** a single task inside a job. Steps run **sequentially** and share the runner's filesystem. Each step is either a shell command (`run:`) or an action (`uses:`).

```yaml
name: First Workflow
on: workflow_dispatch
jobs:
  first-job:
    runs-on: ubuntu-latest
    steps:
      - name: Print greeting
        run: echo "Hello World"
      - name: Print Goodbye
        run: echo "Done - bye!"
```

#### YAML keys explained

| Key | Level | Required? | What it does |
|---|---|---|---|
| `name` | Workflow | No | Display name in the Actions tab. Defaults to the file path if omitted. |
| `on` | Workflow | **Yes** | Event(s) that trigger the workflow (e.g. `workflow_dispatch`, `push`, `pull_request`, `schedule`). |
| `jobs` | Workflow | **Yes** | Container for all jobs in the workflow. |
| `first-job` | Job | **Yes** (≥1 job) | Job ID you choose. Must be unique; letters, numbers, `-`, `_` only; starts with a letter or `_`. |
| `runs-on` | Job | **Yes** | Runner the job runs on (e.g. `ubuntu-latest`, `windows-latest`, `[self-hosted, linux]`). Each job gets a fresh runner. |
| `steps` | Job | **Yes** | Ordered list of tasks. Each `-` is one step. |
| `name` | Step | No | Label for the step in run logs. Defaults to the command (e.g. `Run echo "Hello World"`). |
| `run` | Step | One of `run`/`uses` | Runs shell commands (bash on Linux/macOS, PowerShell on Windows). |

- **`on:` formats:** a single event (`on: push`), a list (`on: [push, workflow_dispatch]`), or a map with filters (`on: push: branches: [main]`).
- **`name:` appears twice** with different meanings: the top-level one names the workflow, and the one inside a step names that step.
- **`steps` share state:** files created by one step are available to the next. If a step fails, the steps after it are skipped by default.
- **Multi-line `run`:**
  ```yaml
  run: |
    echo "line 1"
    echo "line 2"
  ```

**Structure:**
```
workflow (name, on, jobs)
└── job: first-job (runs-on, steps)
    ├── step 1 (name, run)
    └── step 2 (name, run)
```

**Key points**
- `workflow_dispatch` = manual trigger. The workflow file must be on the default branch for the button to show up.
- `on:` can take multiple events: `on: [push, workflow_dispatch]`
- `run:` executes shell commands. For multiple lines, use `run: |`
- `uses:` runs a prebuilt action, e.g. `uses: actions/checkout@v4`. The runner starts empty, so you need checkout to get your repo's code.
- A step uses either `run:` or `uses:`, never both.
- If `name:` is left out, GitHub shows the file path (workflow) or the job ID (job) instead.