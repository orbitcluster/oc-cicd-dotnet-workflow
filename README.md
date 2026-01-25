# OC CICD .NET Workflow

This repository provides reusable CI/CD components designed to streamline the build, test, security scanning, and deployment preparation for .NET applications.

It offers two primary ways to consume these features:

1.  **Composite Action (`action.yml`)**: A focused action for building, testing, and performing basic checks.
2.  **Reusable Workflow (`all-ci.yml`)**: A comprehensive CI pipeline that orchestrates multiple jobs including advanced security scans and Docker builds.

---

## 1. Reusable Workflow (`all-ci.yml`)

The reusable workflow is a complete CI pipeline that runs on `ubuntu-latest`. It orchestrates the following parallel and sequential jobs:

- **Secrets Check**: Scans for hardcoded secrets.
- **SonarQube Check**: Performs code quality and security analysis.
- **SAST Scan**: Runs Static Application Security Testing for C#.
- **Docker Build**: Builds and pushes a Docker image (runs after successful checks).

### Inputs

| Input               | Description                                | Required | Default                    | Type     |
| :------------------ | :----------------------------------------- | :------- | :------------------------- | :------- |
| `app-name`          | Name of the image/app                      | `false`  | `${{ github.repository }}` | `string` |
| `appid`             | Application ID                             | `false`  | `"0"`                      | `string` |
| `orgid`             | Organization ID                            | `false`  | `"0"`                      | `string` |
| `buid`              | Business Unit ID                           | `false`  | `"0"`                      | `string` |
| `test-directory`    | Directory containing tests                 | `false`  | `"test"`                   | `string` |
| `sonar-source-path` | Path to the source code for SonarQube scan | `false`  | `"src"`                    | `string` |
| `dotnet-version`    | .NET SDK version to use                    | `false`  | `"8.0.x"`                  | `string` |
| `solution-path`     | Path to the solution or project file       | `false`  | `.`                        | `string` |

### Usage

To use this workflow in your repository, create a workflow file (e.g., `.github/workflows/ci.yml`) and reference it:

```yaml
name: CI Pipeline

on:
  push:
    branches: ["main"]
  pull_request:
    branches: ["main"]

jobs:
  ci:
    name: Run CI Pipeline
    uses: orbitcluster/oc-cicd-dotnet-workflow/.github/workflows/all-ci.yml@v1
    with:
      app-name: "my-dotnet-app"
      dotnet-version: "8.0.x"
      solution-path: "src"
    secrets: inherit
```

---

## 2. Composite Action (`action.yml`)

The "Build and Test" composite action performs the following operations in a single job step:

1.  **Checkout**: Checks out the repository code.
2.  **Setup .NET**: Sets up the .NET SDK environment.
3.  **Restore Dependencies**: Runs `dotnet restore`.
4.  **Build**: Builds the solution in Release configuration.
5.  **Test**: Runs `dotnet test` (skipped if the test directory doesn't exist).
6.  **Secrets Check**: Basic secret scanning.
7.  **SonarQube Check**: Basic code quality analysis.

### Inputs

| Input               | Description                          | Required | Default |
| :------------------ | :----------------------------------- | :------- | :------ |
| `dotnet-version`    | .NET SDK version to use              | `false`  | `8.0.x` |
| `solution-path`     | Path to the solution or project file | `false`  | `.`     |
| `test-project-path` | Path to the test project             | `false`  | `test`  |

### Usage

To use this action as a step in your own workflow:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: orbitcluster/oc-cicd-dotnet-workflow@v1
        with:
          dotnet-version: "8.0.x"
          solution-path: "src/MyProject"
          test-project-path: "tests/MyProject.Tests"
```
