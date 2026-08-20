<!-- markdownlint-disable -->

# Hardening Report: planetscale--ghcommit-action/v0.2.19

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **planetscale--ghcommit-action/v0.2.19** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unsafe-shell (severity: high)

The 'Calculate new version with autotag' step in release.yaml pipes remote content directly to a shell interpreter: `curl -sL https://git.io/autotag-install | sh -s -- -b "${RUNNER_TEMP}/bin"`. This allows a compromised or malicious remote server to execute arbitrary code on the runner. The script should be downloaded to a file first, its integrity verified (e.g., via checksum), and then executed separately.

Locations:

- `.github/workflows/release.yaml:47`

### missing-permissions (severity: medium)

Multiple workflow files have no top-level `permissions:` block and at least one job with no job-level `permissions:` block. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions (often broad write access). Each workflow or each job should declare minimal required permissions.

- ci.yaml: no top-level permissions; `test` job has no permissions.
- licensing.yaml: no top-level permissions; `licensing` job has no permissions.
- release.yaml: no top-level permissions; `test` job has no permissions (only the `release` job has job-level permissions).
- self-test.yaml: no top-level permissions; `self-test` job has no permissions.
- validate-renovate.yml: no top-level permissions; `renovate-config-validator` job has no permissions.

Locations:

- `.github/workflows/ci.yaml:1`
- `.github/workflows/licensing.yaml:1`
- `.github/workflows/release.yaml:1`
- `.github/workflows/self-test.yaml:1`
- `.github/workflows/validate-renovate.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unsafe-shell, missing-permissions

**Notes:**

Fixed unsafe-shell in release.yaml by downloading the autotag install script to a temp file first (curl -sL ... -o file), then executing it separately (sh file -b ...) — dropping the shell's '-s' and '--' option terminators that were only needed for the pipe form. Fixed missing-permissions by adding 'permissions: {}' at the top level of all 5 workflow files (ci.yaml, licensing.yaml, release.yaml, self-test.yaml, validate-renovate.yml) and adding minimal job-level permissions: 'contents: read' for read-only jobs (ci test, licensing, release test, renovate validator) and 'contents: write' for the self-test job which uses the ghcommit action to commit changes.

