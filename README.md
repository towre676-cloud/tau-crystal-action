# tau-crystal-verify

Receipt-first, Merkle-rooted attestation for GitHub Actions via pure bash. The action measures itself and your repository, emits a chained SHA-256 receipt, and fails if any measured byte drifts. Runs on ubuntu-latest with no containers, no extra runtimes, and no privileged tokens.

## Quick start

Add a step to your workflow that references this action at a tagged release. It writes a JSON receipt and exits nonzero if the wrapper, repository tree digest, or commit reference disagree with expectations.

```yaml
jobs:
  attest:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: towre676-cloud/tau-crystal-action@v1.0.0
        with:
          working-directory: .  # repository root to measure
          out: .tau_ledger/receipt.json
      - name: verify receipt
        run: bash verify/verify-receipt.sh .tau_ledger/receipt.json
```

## What it records

The receipt binds the exact Git commit, a Merkle-style tree digest over tracked files, and the digest of the wrapper that produced the run. Because the wrapper hash is included in the receipt, any silent edit to the action metadata or entrypoint causes the next run to fail.

## Outputs and verification

The JSON receipt is plain text and ships with a pinned schema in this repository. A bash verifier is included so downstream jobs can recheck the receipt before deploy with no extra tools.
