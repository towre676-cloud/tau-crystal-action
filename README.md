# tau-crystal-verify

Prove what ran: receipt-first attestation for GitHub Actions in pure bash. It measures itself and your repository, emits a chained SHA-256 receipt, and fails if any byte drifts. Runs on ubuntu-latest with no containers, no extra runtimes, and no privileged tokens.

## Quick start

Drop this into any workflow. It writes a JSON receipt and exits non-zero if the wrapper, tree digest, or commit deviates.

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
        run: bash verify/verify-receipt.sh \\
               .tau_ledger/receipt.json
```

## What it records

The receipt binds the commit ID, a Merkle tree over tracked files, and the digest of the wrapper that produced the run. Because that digest is sealed into the receipt, any silent edit to the action metadata or entrypoint breaks the chain on the next run.

## Outputs and verification

The receipt is plain JSON and ships with a pinned schema in this repository. A bash verifier lets downstream jobs recheck it before deploy with zero extra tools.

## Inputs

| Name               | Required | Default | Description                              |
|--------------------|---------:|:-------:|------------------------------------------|
| `working-directory`|   no     |   `.`   | Repository root to measure                |
| `out`              |   no     |   —     | Write receipt to this path (JSON file)    |
| `install-elan`     |   no     |  `false`| Install Lean toolchain for proof kernel   |

## Outputs

| Name          | Description                                           |
|---------------|-------------------------------------------------------|
| `receipt`     | Path to the emitted JSON receipt                      |
| `chain-head`  | SHA-256 of the wrapper recorded in the receipt        |
| `merkle-root` | Tree digest over tracked files for the measured repo  |

## Local verification (optional)

Install the CLI to verify receipts on your machine:
\`curl -sSL https://raw.githubusercontent.com/towre676-cloud/tau-install/main/install.sh | bash\`

Then run:
\`tau verify receipt.json <head> <root>\`
