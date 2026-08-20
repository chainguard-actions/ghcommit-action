<!-- markdownlint-disable -->

# Hardening Report: planetscale--ghcommit-action/v0.2.18

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **planetscale--ghcommit-action/v0.2.18** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unsafe-shell (severity: high)

The 'Calculate new version with autotag' step in release.yaml pipes remote content directly to a shell interpreter: `curl -sL https://git.io/autotag-install | sh -s -- -b "${RUNNER_TEMP}/bin"`. This allows arbitrary code execution if the remote URL is compromised or redirected. The script should be downloaded to a file first, its integrity verified (e.g., via checksum), and then executed separately.

Locations:

- `.github/workflows/release.yaml:52`

### missing-permissions (severity: medium)

Multiple workflow files have no top-level `permissions:` block and at least one job also lacks a job-level `permissions:` block, meaning jobs run with the default (potentially broad) token permissions.

- ci.yaml: no top-level or job-level permissions on any job.
- licensing.yaml: no top-level or job-level permissions on any job.
- release.yaml: no top-level permissions; the `test` job has no job-level permissions (only the `release` job does).
- self-test.yaml: no top-level or job-level permissions on any job.
- validate-renovate.yml: no top-level or job-level permissions on any job.

Each workflow (or every job within it) should declare minimal required permissions explicitly.

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

Fixed two security findings:

1. unsafe-shell (release.yaml): Replaced `curl -sL https://git.io/autotag-install | sh -s -- -b "${RUNNER_TEMP}/bin"` with a two-step approach: download the script to `${RUNNER_TEMP}/autotag-install.sh` first, then execute it with `sh "$INSTALL_SCRIPT" -b "${RUNNER_TEMP}/bin"`. The `--` was dropped as it was the shell's option terminator (from `sh -s --`), not the script's own argument.

2. missing-permissions: Added `permissions: {}` at the top level of all five workflow files (ci.yaml, licensing.yaml, release.yaml, self-test.yaml, validate-renovate.yml) and added minimal job-level permissions to each job: `contents: read` for ci, licensing, and validate-renovate jobs; `contents: read` for the release.yaml test job (release job already had write permissions); `contents: write` for self-test (required by ghcommit-action to commit changes).

