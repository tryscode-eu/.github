# Reusable CI foundation

This repository provides a small common CI foundation for TrysCode repositories:

- `ci-planned.yml` checks the baseline of a repository that is not active yet;
- `ci-python.yml` runs locked installation, formatting, linting, compilation, optional typing, and tests;
- `ci-native.yml` runs configurable formatting, configuration, compilation, and tests for C/C++ projects;
- `ci-content.yml` validates JSON, YAML, whitespace, and optional repository-specific content checks;
- `candidate-image.yml` builds a candidate on pull requests, then publishes the
  SHA-tagged image only for a push on the caller repository's default branch;
- `staging-release.yml` validates an immutable image and, only behind an
  explicit gate, invokes the bounded `release-v1` protocol through a dedicated
  staging identity.

All third-party actions are pinned to full commit SHAs. CI workflows receive
only `contents: read`; only the candidate-image workflow receives
`packages: write`. The staging release is a separate, explicit boundary: its
contract job needs no secret, and its deployment job can access only the
caller's protected `staging` environment.

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

The release workflow is documented in [REUSABLE_CD.md](REUSABLE_CD.md). It does
not make a repository `cd_ready`: the dedicated forced SSH identity, dispatcher,
registry reachability, smoke test and rollback must be qualified first.
