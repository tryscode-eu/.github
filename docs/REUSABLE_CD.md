# Reusable staging release

`staging-release.yml` is the common CD boundary for repositories whose image,
runtime identity and rollback have already been qualified. It never accepts a
shell command, a manifest or a script from a caller. The only remote request is:

```text
release-v1 <allowlisted-service> <ghcr-image@sha256:digest> <git-sha>
```

The caller must pin this repository to an immutable commit SHA and pass
`deploy-enabled: true` only after its staging environment is ready. A false gate
validates the public release inputs and makes no network connection.

## Required staging secrets

The caller repository's `staging` environment owns these logical secrets:

- `SHERLOCK_HOST`;
- `SHERLOCK_PORT`;
- `SHERLOCK_USER`;
- `SHERLOCK_SSH_PRIVATE_KEY`;
- `SHERLOCK_KNOWN_HOSTS`.

Values never belong in Git. `SHERLOCK_SSH_PRIVATE_KEY` must be a dedicated key,
not a copied personal key. Its public half must be installed with an
`authorized_keys` forced command bound to exactly one service. The known-hosts
entry must be captured through an independently verified Sherlock host key;
the workflow intentionally does not use `ssh-keyscan`.

The declarations are optional at the reusable-workflow boundary so a disabled
release can validate its public contract with no staging access. The deploy job
fails closed on every missing value when `deploy-enabled` is true.

## Dispatcher contract

The forced dispatcher is responsible for all Kubernetes mutation. Before a key
is enabled, its implementation and installation must prove that it:

1. rejects every protocol other than `release-v1` and every non-allowlisted
   service, image registry, digest or revision;
2. binds the SSH public key to one service and cannot execute a generic shell,
   `kubectl exec`, Secret read or arbitrary pod operation;
3. records the currently healthy revision before touching the candidate;
4. deploys only the supplied immutable image digest;
5. waits for rollout, liveness and readiness, then runs the service's bounded
   smoke test;
6. keeps or restores the prior healthy revision on every failure;
7. returns a bounded, redacted result and removes temporary files.

Until that server contract and the cluster's access to GHCR are proven for a
repository, the caller must leave `deploy-enabled` false and must not report a
deployment.

## Caller example

```yaml
jobs:
  release:
    if: github.event_name == 'push' && github.ref_name == github.event.repository.default_branch
    uses: tryscode-eu/.github/.github/workflows/staging-release.yml@<full-commit-sha>
    with:
      service-name: pws-example
      image: ghcr.io/tryscode-eu/pws-example@sha256:<64-hex-digest>
      deploy-enabled: false
    secrets: inherit
```

The image value must come from the candidate-image job's `immutable-image`
output. That output is produced only after publication and has the form
`ghcr.io/<owner>/<image>@sha256:<digest>`; a mutable tag such as `latest` is
rejected.
