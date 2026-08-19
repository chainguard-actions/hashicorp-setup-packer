<!-- markdownlint-disable -->

# Hardening Report: hashicorp--setup-packer/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **hashicorp--setup-packer/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a) violation: The expression `${{ env.PRODUCT_VERSION }}` is interpolated directly inside a `run:` shell command string. Even though `env.PRODUCT_VERSION` is set to a literal value in this workflow, `env.*` context values flow through YAML template substitution before the shell ever sees them, making this a script-injection risk. The offending line is: `run: "packer version | grep --silent 'Packer v${{ env.PRODUCT_VERSION }}'"`. The safe fix is to reference the env var as a shell variable (`$PRODUCT_VERSION`) instead of using the `${{ }}` expression syntax inside the run block.

Locations:

- `.github/workflows/actions-self-test.yml:24`

### missing-permissions (severity: medium)

None of the three workflow files define a top-level `permissions:` key, and none of their jobs define job-level `permissions:` keys. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions (often `write-all` for older repos), granting unnecessarily broad access. Each workflow should declare minimal required permissions (e.g., `permissions: read-all` or specific scopes like `contents: read`).

Locations:

- `.github/workflows/actions-self-test.yml:1`
- `.github/workflows/superlinter.yml:1`
- `.github/workflows/typescript.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

1. Fixed script-injection in .github/workflows/actions-self-test.yml: replaced `${{ env.PRODUCT_VERSION }}` inside the `run:` shell string with the shell variable `$PRODUCT_VERSION`. The env var is already defined at the workflow level and is available to the shell without template interpolation.
2. Added `permissions: {}` at the top level of all three workflow files (actions-self-test.yml, superlinter.yml, typescript.yml) to satisfy the missing-permissions finding. None of these workflows require any GitHub token permissions, so an empty permissions block is the correct minimal grant.

