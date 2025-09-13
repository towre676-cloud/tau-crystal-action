# tau-crystal-action

![tau-crystal verified](https://img.shields.io/badge/receipt-verified-brightgreen)

![tau-crystal verified](https://img.shields.io/badge/receipt-verified-brightgreen)

Prove what ran — receipt-first, Merkle-rooted attestation for GitHub Actions in pure bash.

### One-liner (optional local verifier)
`curl -sSL https://raw.githubusercontent.com/towre676-cloud/tau-install/main/install.sh | bash`

### 30-second usage (zero config)
Add a step; it measures itself + your repo, writes a JSON receipt, and fails on drift.

```yaml
jobs:
  attest:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - id: tau
        uses: towre676-cloud/tau-crystal-action@v1
        with:
          working-directory: .   # repo root to measure

      # (Optional) verify before deploy if you installed the CLI
      - name: verify receipt
        run: tau verify "${{ steps.tau.outputs.receipt }}" \
                     "${{ steps.tau.outputs.chain-head }}" \
                     "${{ steps.tau.outputs.merkle-root }}"
```

### Why it matters
Emits a chained SHA-256 receipt binding the exact commit, Merkle tree over tracked files, and the digest of the wrapper that ran. Any silent edit (YAML/entrypoint/tag) breaks the chain on the next run.

### Inputs / Outputs
- **input**: `working-directory` (default `.`)
- **outputs**: `receipt` (JSON path), `chain-head` (commit), `merkle-root` (tree digest)

### Demos & docs
See full monographs and examples: https://github.com/towre676-cloud/tau_crystal

JSON schema: [schema-action-outputs.json](schema-action-outputs.json) — IDE autocomplete ready.

Deep dive & physics demos: [towre676-cloud/tau_crystal](https://github.com/towre676-cloud/tau_crystal)
