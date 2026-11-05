# dev-lab

A personal engineering lab for documenting problems, solutions, experiments, technical research, and lessons learned across software development, AI, DevOps, ERP, infrastructure, and technology.

## Central GitHub Actions CI

This repository is the canonical home of reusable CI workflows for repositories owned by `atilioobadia-cpu`.

### Reusable workflow

- Workflow: `.github/workflows/reusable-ci.yml`
- Reference: `atilioobadia-cpu/dev-lab/.github/workflows/reusable-ci.yml@main`
- Invocation: `workflow_call`
- Runs on Ubuntu hosted runners.
- Uses read-only repository permissions.

### Standard caller workflow

Add `.github/workflows/ci.yml` to a repository that does not already have a suitable CI workflow:

```yaml
name: CI

on:
  push:
  pull_request:
  workflow_dispatch:

permissions:
  contents: read

jobs:
  repository-ci:
    name: Reusable repository checks
    uses: atilioobadia-cpu/dev-lab/.github/workflows/reusable-ci.yml@main
```

### Checks performed

- **Repository integrity:** verifies tracked files exist and `git diff --check` passes.
- **Node.js:** installs from npm, pnpm, or Yarn lockfiles; runs configured lint (when ESLint config exists), typecheck, real test, and build scripts.
- **Python:** compiles Python sources; runs pytest only when an explicit pytest configuration exists and the project is not a Frappe app. Frappe apps need a configured Bench environment for integration tests.
- **PHP:** validates Composer metadata/lock consistency and lints PHP files.

The workflow deliberately does not run placeholder npm test scripts, generic Frappe tests outside Bench, or tests that require physical audio/video devices. Project-specific test environments should be added as dedicated reusable workflows rather than weakening these checks.

### Adoption policy

- New repositories should use this reusable workflow unless a framework-specific workflow already provides better coverage.
- Existing project-specific workflows are retained; do not replace them without reviewing their jobs and required checks.
- Caller workflows track `@main`, so improvements here are picked up on the next run.
- CI runs on pushes and pull requests. Configure branch protection separately after confirming stable required check names and administrator access.
