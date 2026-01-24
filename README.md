# OC CICD .NET Workflow

This is a reusable composite GitHub Action designed to streamline the build, test, and security scanning process for .NET applications. It encapsulates common CI/CD steps into a single, easy-to-use action.

## Description

The "Build and Test" action performs the following operations:

1.  **Checkout**: Checks out the repository code.
2.  **Setup .NET**: Sets up the .NET SDK environment using the specified version (default: 8.0.x).
3.  **Restore Dependencies**: Runs `dotnet restore` to restore NuGet packages.
4.  **Build**: Builds the solution in Release configuration.
5.  **Test**: Runs tests using `dotnet test` (skipped if test directory doesn't exist).
6.  **Secrets Check**: Scans the codebase for hardcoded secrets using `orbitcluster/oc-cicd-secretscanner-workflow` (Runs on `push` and `pull_request`).
7.  **SonarQube Check**: Performs a SonarQube code quality analysis using `orbitcluster/oc-cicd-sonarscan-workflow` (Runs on `push` and `pull_request`).

## Inputs

| Input               | Description                          | Required | Default |
| :------------------ | :----------------------------------- | :------- | :------ |
| `dotnet-version`    | .NET SDK version to use              | `false`  | `8.0.x` |
| `solution-path`     | Path to the solution or project file | `false`  | `.`     |
| `test-project-path` | Path to the test project             | `false`  | `test`  |

## Usage

To use this action in your workflow, map the `uses` keyword to the location of this action.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: orbitcluster/oc-cicd-dotnet-workflow@v1
        with:
          dotnet-version: "8.0.x"
          solution-path: "src/webApp"
          test-project-path: "test"
```

## Workflow Steps Details

### 1. Checkout

Uses `actions/checkout@v4` to fetch the source code.

### 2. Set up .NET

Uses `actions/setup-dotnet@v4`.

- **Version**: Configurable via `dotnet-version` input (default: 8.0.x).

### 3. Restore Dependencies

Executes `dotnet restore` on the specified solution path.

### 4. Build

Executes `dotnet build` in Release configuration with `--no-restore` flag.

### 5. Test

Executes `dotnet test` if the test directory exists, otherwise skips.

### 6. Security & Quality Checks

- **Secrets Check**: Triggers on `push` and `pull_request` events to ensure no sensitive data is committed.
- **SonarQube Check**: Triggers on `push` and `pull_request` events to analyze code quality in the `src` directory.
