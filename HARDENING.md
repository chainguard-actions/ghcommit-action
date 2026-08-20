<!-- markdownlint-disable -->

# Hardening Report: planetscale--ghcommit-action/v0.2.16

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **planetscale--ghcommit-action/v0.2.16** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unsafe-shell (severity: high)

The 'Calculate new version with autotag' step in release.yaml pipes a remote install script directly to `sh` without first downloading and inspecting it: `curl -sL https://git.io/autotag-install | sh -s -- -b "${RUNNER_TEMP}/bin"`. If the remote URL is compromised or redirected, arbitrary code will execute on the runner.

Locations:

- `.github/workflows/release.yaml:47`

### missing-permissions (severity: medium)

ci.yaml has no top-level `permissions:` key and its only job (`test`) also has no job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository default (typically `contents: write` for private repos or broad defaults), violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yaml:1`

### missing-permissions (severity: medium)

licensing.yaml has no top-level `permissions:` key and its only job (`licensing`) also has no job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository default, violating the principle of least privilege.

Locations:

- `.github/workflows/licensing.yaml:1`

### missing-permissions (severity: medium)

self-test.yaml has no top-level `permissions:` key and its only job (`self-test`) also has no job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository default, violating the principle of least privilege.

Locations:

- `.github/workflows/self-test.yaml:1`

### missing-permissions (severity: medium)

validate-renovate.yml has no top-level `permissions:` key and its only job (`renovate-config-validator`) also has no job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository default, violating the principle of least privilege.

Locations:

- `.github/workflows/validate-renovate.yml:1`

### missing-permissions (severity: medium)

release.yaml has no top-level `permissions:` key. The `test` job (line 22) has no job-level `permissions:` block, meaning it runs with the repository default permissions. Only the `release` job has explicit permissions. A top-level `permissions:` block (or per-job permissions on every job) is required.

Locations:

- `.github/workflows/release.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unsafe-shell, missing-permissions

**Notes:**

Fixed 6 findings across 5 workflow files:

1. release.yaml (unsafe-shell): Replaced `curl ... | sh -s -- -b ...` with a two-step approach: download the install script to a temp file with `curl -sL ... -o "$INSTALL_SCRIPT"`, then execute it with `sh "$INSTALL_SCRIPT" -b "${RUNNER_TEMP}/bin"`. The `--` was dropped as it was the shell's option terminator in the pipe form, not an argument to the installer script.

2. release.yaml (missing-permissions): Added `permissions: {}` at the top level and `permissions: contents: read` to the `test` job. The `release` job already had explicit permissions.

3. ci.yaml (missing-permissions): Added `permissions: {}` at the top level and `permissions: contents: read` to the `test` job.

4. licensing.yaml (missing-permissions): Added `permissions: {}` at the top level and `permissions: contents: read` to the `licensing` job.

5. self-test.yaml (missing-permissions): Added `permissions: {}` at the top level and `permissions: contents: read` to the `self-test` job.

6. validate-renovate.yml (missing-permissions): Added `permissions: {}` at the top level and `permissions: contents: read` to the `renovate-config-validator` job.

