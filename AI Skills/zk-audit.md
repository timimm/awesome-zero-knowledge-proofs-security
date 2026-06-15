# zk-audit

## Purpose

Use this skill to run a structured cryptography/ZKP security audit. It organizes specialist review passes, false-positive review, and final report generation for circuit and proof-system projects.

## When to use

Use it when the target contains `.circom`, `.zok`, `.r1cs`, circuit source code, proof-system code, verifier contracts, proving scripts, verification keys, or ZK protocol integration logic.

## Audit tracks

| Track | Output focus |
|---|---|
| Protocol soundness | Whether invalid statements can produce valid proofs |
| Circuit implementation | Constraint completeness, witness assignment, range checks, public inputs, and gadget integration |
| Trusted setup and key management | CRS, toxic waste assumptions, proving/verifying key provenance, verifier artifact integrity |
| On-chain verifier integration | Public input interpretation, verifier invocation, proof replay, root/nullifier handling |
| System integration | SDK, contract, prover, verifier, hash implementation, transcript, and deployment consistency |

## Workflow

1. Preread the project and identify circuit, verifier, setup, and integration files.
2. Load relevant ZK checklists and any existing `overview.md`.
3. Perform specialist review passes for soundness, circuit implementation, setup, verifier, and integration.
4. Re-check high-impact findings against real code paths and retract false positives.
5. Produce a final Chinese report with confirmed findings and concise remediation guidance.

## Finding template

Findings should be concise, evidence-driven, and written in English when used for polished report entries. Questions, Issues, and Recommendations should include a title, description, impact, and suggestion. Notes should include only title and description.

For circuit findings, impact should distinguish module-level effects from system-level effects in one sentence. For example, a public input may be unconstrained at the circuit level but still fail to create a system-level double-spend if the nullifier is unchanged.

## ZK review focus

- Soundness errors, including missing constraints, unconstrained witness assignments, semantic mismatch, and invalid verifier statements.
- Completeness errors, including over-constrained circuits, field/domain mismatch, and application states that cannot be proven.
- Zero-knowledge errors, including missing blinding, private data leakage through public signals, and unsafe transcript or proof objects.
- Cryptographic primitive misuse, including hashes, commitments, nullifiers, Merkle proofs, signatures, elliptic-curve operations, non-native arithmetic, and Fiat-Shamir transcripts.
- Cross-layer integration errors across circuit, SDK, verifier, contract, deployment artifacts, and documented protocol assumptions.

