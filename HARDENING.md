<!-- markdownlint-disable -->

# Hardening Report: jagmarques--asqav-compliance/v1.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jagmarques--asqav-compliance/v1.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow file .github/workflows/ci.yml references actions using mutable tag refs instead of pinned 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the upstream action tag is moved or compromised. Failing references: `actions/checkout@v6` (lines 11, 21) and `actions/setup-node@v6` (lines 12, 22).

Locations:

- `.github/workflows/ci.yml:11`
- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:21`
- `.github/workflows/ci.yml:22`

### missing-permissions (severity: medium)

The workflow file .github/workflows/ci.yml has no top-level `permissions:` key and none of the three jobs (lint, test, ci-ok) define job-level `permissions:` blocks. Without explicit permissions, the workflow inherits the repository default (which may be write-all), granting unnecessarily broad access to the GITHUB_TOKEN.

Locations:

- `.github/workflows/ci.yml:1`

### script-injection (severity: high)

The `ci-ok` job's `run:` block directly interpolates GitHub Actions expressions inside shell command strings (sub-rule a). The expressions `${{ needs.lint.result }}` and `${{ needs.test.result }}` are substituted into the shell script before the shell parses it. Offending lines: `if [ "${{ needs.lint.result }}" != "success" ] || [ "${{ needs.test.result }}" != "success" ]` and `echo "lint=${{ needs.lint.result }} test=${{ needs.test.result }}"`

Locations:

- `.github/workflows/ci.yml:33`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings in .github/workflows/ci.yml: (1) Pinned actions/checkout@v6 to SHA d23441a48e516b6c34aea4fa41551a30e30af803 and actions/setup-node@v6 to SHA 249970729cb0ef3589644e2896645e5dc5ba9c38 at all four occurrences (lines 11, 12, 21, 22), preserving the tag as a comment. (2) Added top-level `permissions: {}` to restrict GITHUB_TOKEN to no permissions by default. (3) Moved `${{ needs.lint.result }}` and `${{ needs.test.result }}` expressions from the ci-ok job's run script into the step's env block as LINT_RESULT and TEST_RESULT, then referenced them as plain shell variables to prevent script injection.

