# Reusable CI foundation

This repository provides a small common CI foundation for TrysCode repositories:

- `ci-planned.yml` checks the baseline of a repository that is not active yet;
- `ci-python.yml` runs locked installation, formatting, linting, compilation, optional typing, and tests;
- `ci-native.yml` runs configurable formatting, configuration, compilation, and tests for C/C++ projects;
- `ci-content.yml` validates JSON, YAML, whitespace, and optional repository-specific content checks;
- `candidate-image.yml` builds a candidate on pull requests, then publishes the
  SHA-tagged image only for a push on the caller repository's default branch;

All third-party actions are pinned to full commit SHAs. CI workflows receive only `contents: read`; only the candidate-image workflow receives `packages: write`. None of these workflows deploys a service or accepts a deployment secret.

Call a workflow from a lightweight repository workflow and pin the organization workflow to an immutable commit SHA:

```yaml
jobs:
  ci:
    uses: tryscode-eu/.github/.github/workflows/ci-python.yml@<full-commit-sha>
    with:
      python-version: "3.12"
      install-command: python -m pip install --require-hashes -r requirements.lock
```

Commands are supplied by the caller configuration and run with no staging or production secrets. Keep dependency installation locked and use the candidate image output only as an unpromoted deployment candidate.
