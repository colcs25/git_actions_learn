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