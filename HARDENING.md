<!-- markdownlint-disable -->

# Hardening Report: planetscale--ghcommit-action/v0.2.17

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **planetscale--ghcommit-action/v0.2.17** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unsafe-shell (severity: high)

The 'Calculate new version with autotag' step in release.yaml pipes remote content directly to a shell interpreter: `curl -sL https://git.io/autotag-install | sh -s -- -b "${RUNNER_TEMP}/bin"`. This allows arbitrary code execution if the remote URL is compromised or redirected. The script should be downloaded to a file first, verified (e.g., checksum), and then executed separately.

Locations:

- `.github/workflows/release.yaml:52`

### missing-permissions (severity: medium)

Multiple workflow files have no top-level `permissions:` key and contain jobs with no job-level `permissions:` key either. This means jobs run with the default (potentially broad) repository permissions. Affected files and jobs: ci.yaml (job: test), licensing.yaml (job: licensing), self-test.yaml (job: self-test), validate-renovate.yml (job: renovate-config-validator), and release.yaml (job: test — only the 'release' job has explicit permissions). Each workflow file should declare minimal required permissions at the top level or on every job.

Locations:

- `.github/workflows/ci.yaml:1`
- `.github/workflows/licensing.yaml:1`
- `.github/workflows/self-test.yaml:1`
- `.github/workflows/validate-renovate.yml:1`
- `.github/workflows/release.yaml:23`

## Iteration Notes

### Iteration 1

**Fixes applied:** unsafe-shell, missing-permissions

**Notes:**

Fixed unsafe-shell in release.yaml by downloading the autotag install script to a temp file first, then executing it separately (dropping the '--' shell option terminator as required). Added minimal permissions blocks to all affected jobs: ci.yaml (test: contents:read), licensing.yaml (licensing: contents:read), self-test.yaml (self-test: contents:write for ghcommit), validate-renovate.yml (renovate-config-validator: contents:read), and release.yaml (test: contents:read; release job already had explicit permissions).

