<!-- markdownlint-disable -->

# Hardening Report: hashicorp--setup-packer/v2.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **hashicorp--setup-packer/v2.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is interpolated directly inside a run: shell command string. The step 'Validate `packer` version is accurate' contains: run: "packer version | grep --silent 'Packer v${{ env.PRODUCT_VERSION }}'" — the expression ${{ env.PRODUCT_VERSION }} is expanded by the Actions template engine before the shell ever sees the string, making it a script-injection vector. The value should be read from the env: context as a shell variable (e.g. $PRODUCT_VERSION) instead.

Locations:

- `.github/workflows/actions-self-test.yml:30`

### missing-permissions (severity: medium)

None of the three workflow files define a top-level permissions: key, and none of their jobs define a job-level permissions: key. Without explicit permissions, GitHub Actions grants the default token permissions (which may include write access to contents, packages, etc. depending on repository settings). Each workflow should declare minimal required permissions.

Locations:

- `.github/workflows/actions-self-test.yml:1`
- `.github/workflows/superlinter.yml:1`
- `.github/workflows/typescript.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

1. script-injection (actions-self-test.yml line 30): Moved `${{ env.PRODUCT_VERSION }}` out of the `run:` shell string into the step's `env:` block as `PRODUCT_VERSION: ${{ env.PRODUCT_VERSION }}`. The shell command now uses `${PRODUCT_VERSION}` — a plain environment variable — preventing template-engine injection before the shell sees the string.
2. missing-permissions: Added `permissions: {}` at the top level of all three workflow files (actions-self-test.yml, superlinter.yml, typescript.yml) to explicitly restrict the GITHUB_TOKEN to no permissions, satisfying the least-privilege requirement.

