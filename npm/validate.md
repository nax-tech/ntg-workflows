## About

A Github Action Workflow to validate a Javascript project that uses npm for dependency and script management.

## Usage

**With no inputs specified:**
```
jobs:
  validate:
    uses: nax-tech/ntg-workflows/npm/validate.yaml@v1
```

**With codebase in a specific subdirectory:**
```
jobs:
  validate:
    uses: nax-tech/ntg-workflows/npm/validate.yaml@v1
    with:
      directory: subdirectory
```

**With custom installation command:**
```
jobs:
  validate:
    uses: nax-tech/ntg-workflows/npm/validate.yaml@v1
    with:
      install_command: npm install --legacy-peer-deps
```

## Inputs

| name | required | description |
| --- | --- | --- |
| `directory` | false | The directory where all commands should be run. (Default: root of the repository) |
| `node_version` | false | The version of NPM to install. (Default: 16.14.2) |
| `install_command` | false | The command to run to install all required NPM packages. (Default: `npm install`) |
| `lint_command` | false | The command to run to lint the codebase. (Default: `npm run lint`) |
| `test_command` | false | The command to run tests against the codebase. (Default: `npm run test -- --passWithNoTests`) |

## Secrets

None

## Outputs

| name | description |
| --- | --- |
| `install` | The result of the install job. This can be included in _slack/notify.yaml_ to insert the standard emoji. |
| `lint` | The result of the lint job. This can be included in _slack/notify.yaml_ to insert the standard emoji. |
| `test` | The result of the test job. This can be included in _slack/notify.yaml_ to insert the standard emoji. |
