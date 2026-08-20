<!-- markdownlint-disable -->

# Hardening Report: planetscale--ghcommit-action/v0.2.20

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **planetscale--ghcommit-action/v0.2.20** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unsafe-shell (severity: high)

The 'Calculate new version with autotag' step in release.yaml pipes remote content directly to a shell interpreter: `curl -sL https://git.io/autotag-install | sh -s -- -b "${RUNNER_TEMP}/bin"`. This allows arbitrary code execution if the remote URL is compromised or redirected.

Locations:

- `.github/workflows/release.yaml:38`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` block and at least one job also lacks a job-level `permissions:` block, meaning jobs run with the default (potentially broad) GITHUB_TOKEN permissions: ci.yaml (test job), licensing.yaml (licensing job), self-test.yaml (self-test job), validate-renovate.yml (renovate-config-validator job), and release.yaml (test job — only the release job has permissions defined).

Locations:

- `.github/workflows/ci.yaml:1`
- `.github/workflows/licensing.yaml:1`
- `.github/workflows/self-test.yaml:1`
- `.github/workflows/validate-renovate.yml:1`
- `.github/workflows/release.yaml:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** unsafe-shell, missing-permissions

**Notes:**

Fixed unsafe-shell in release.yaml by downloading the autotag install script to a temp file before executing it (dropped the '--' shell option terminator as required). Added top-level 'permissions: {}' to all 5 workflow files (release.yaml, ci.yaml, licensing.yaml, self-test.yaml, validate-renovate.yml) and added job-level permissions blocks to all jobs that lacked them: test jobs get 'contents: read', self-test gets 'contents: write' (needed for GITHUB_TOKEN commit), and the release job already had its own permissions.

