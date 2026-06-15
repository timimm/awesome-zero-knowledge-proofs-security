# Frozen Heart

## Introduction

Frozen Heart is a Fiat-Shamir soundness failure where the transcript does not bind the full statement and all proof-relevant commitments. If a prover can reuse or manipulate challenges across different statements, the non-interactive proof may verify without proving the intended relation.

## Cases

### 1. Bulletproofs and PlonK: Forging zero-knowledge proofs

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| Trail of Bits Team | High | [Bulletproofs Paper: Frozen Heart](https://github.com/0xPARC/zk-bug-tracker#11-bulletproofs-paper-frozen-heart) | A proof system transcript could be manipulated to forge proofs for false statements. |

#### Description

The Frozen Heart issue affected proof systems that applied Fiat-Shamir without binding enough protocol context into the challenge derivation. The transcript must commit to the statement, commitments, public inputs, and all values that influence verification. If any security-relevant value is missing from the challenge computation, an attacker may construct a proof that verifies for a statement that was not actually proven.

### 2. Aztec Protogalaxy: Recursive verifier transcript bug

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| Aztec | High | [Protogalaxy recursive verifier transcript bug](https://github.com/AztecProtocol/barretenberg/blob/master/security/descriptions/Protogalaxy%20recursive%20verifier%20transcript%20bug.md) | Recursive verification could accept proofs under an incomplete transcript binding. |

#### Description

Recursive verifiers must reproduce the same Fiat-Shamir transcript that the native verifier expects. If the recursive circuit omits a commitment, public input, domain separator, or proof element from the transcript, the recursive proof may verify a weaker statement than the base proof system. This makes transcript reconstruction a first-class audit target in recursive SNARK designs.

## References

- [Trail of Bits Blog](https://blog.trailofbits.com/2022/04/13/part-1-coordinated-disclosure-of-vulnerabilities-affecting-girault-bulletproofs-and-plonk/)
- [Aztec Protogalaxy Transcript Bug](https://github.com/AztecProtocol/barretenberg/blob/master/security/descriptions/Protogalaxy%20recursive%20verifier%20transcript%20bug.md)
