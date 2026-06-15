# Cryptographic Primitive Misuse

- [Introduction](#introduction)
- [Common patterns](#common-patterns)
- [Cases](#cases)
  - [1. Aleo: Non-committing encryption in private input IDs](#1-aleo-non-committing-encryption-in-private-input-ids)
  - [2. Polygon zkEVM: Ambiguous sparse Merkle tree inclusion](#2-polygon-zkevm-ambiguous-sparse-merkle-tree-inclusion)
  - [3. Aztec Plonk verifier: Missing curve point handling](#3-aztec-plonk-verifier-missing-curve-point-handling)
- [Review checklist](#review-checklist)
- [Related bugs](#related-bugs)

## Introduction

Cryptographic primitive misuse in circuits happens when a primitive is secure in isolation but is used with the wrong construction, context, encoding, parameter set, or integration boundary. In ZK systems this can break soundness, completeness, or privacy depending on whether the primitive binds statements, authorizes ownership, identifies nullifiers, builds Merkle roots, verifies signatures, or hides private values.

This class is difficult to detect manually because the circuit may compile and the primitive may be cryptographically strong, while the surrounding protocol statement is still wrong.

## Common patterns

- Hash functions with missing domain separation, ambiguous encoding, unsafe parameters, or cross-layer implementation drift.
- Commitments that omit required fields, reuse blinding incorrectly, or do not match the application statement.
- Nullifiers that do not bind enough context to prevent replay or double-spending.
- Merkle proofs with ambiguous leaf/internal node domains, inconsistent zero values, or mismatched path conventions.
- Signature verification circuits that omit public-key checks, range checks, malleability checks, or final validity constraints.
- Elliptic-curve circuits that omit curve membership, subgroup membership, identity-point handling, scalar range checks, or exceptional cases.
- Non-native arithmetic circuits with incomplete limb range checks, carry constraints, modular reductions, or canonical output checks.
- Fiat-Shamir or recursive verifier circuits that do not bind all public inputs, commitments, and transcript context.
- Encryption or note-delivery logic that is not bound to the proved commitment or recipient assumptions.
- Placeholder or non-cryptographic implementations reachable from production paths.

## Cases

### 1. Aleo: Non-committing encryption in private input IDs

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| zkSecurity | High | [Aleo: Non-Committing Encryption Used in InputID::Private](https://github.com/0xPARC/zk-bug-tracker#25-aleo-non-committing-encryption-used-in-inputidprivate) | A ciphertext could be opened with ambiguous semantics, weakening the binding between private data and the proved statement. |

#### Description

The Aleo issue shows that encryption inside a ZK protocol is not automatically a commitment to the encrypted message. If the protocol uses an encryption output as an identifier or binding value, the encryption mode must be committing or otherwise bound to the full statement. Otherwise, different plaintext or randomness interpretations may remain consistent with the same protocol object, and the surrounding proof can verify a statement that is not uniquely tied to the intended private input.

### 2. Polygon zkEVM: Ambiguous sparse Merkle tree inclusion

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| Hexens | High | [Polygon zkEVM: Missing constraint in PIL leading to proving fake inclusion in the SMT](https://github.com/0xPARC/zk-bug-tracker#21-polygon-zkevm-missing-constraint-in-pil-leading-to-proving-fake-inclusion-in-the-smt) | A prover could prove an invalid sparse Merkle tree inclusion and corrupt state-transition validity. |

#### Description

The Polygon zkEVM storage state machine used a sparse Merkle tree to prove key-value inclusion. The vulnerability came from missing constraints that should bind the reconstructed key/path data to the value passed from the execution layer. This is a Merkle primitive misuse at the protocol boundary: the root check alone is not sufficient unless the leaf encoding, path bits, remaining key, and execution-layer key are all bound by constraints.

### 3. Aztec Plonk verifier: Missing curve point handling

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| Nguyen Thoi Minh Quan | High | [Aztec Plonk Verifier: 0 Bug](https://github.com/0xPARC/zk-bug-tracker#7-aztec-plonk-verifier-0-bug) | An invalid proof could be accepted by exploiting unchecked zero-valued proof elements. |

#### Description

The Aztec Plonk verifier accepted malformed proof elements because special curve-point or zero-value cases were not handled safely. This kind of bug is a cryptographic primitive misuse rather than a business-logic mistake: verifier code must reject invalid curve points, identity points, non-canonical encodings, and exceptional values before using them in algebraic checks.

## Review checklist

1. Identify the security role of each primitive in the statement.
2. Check that each role has explicit domain separation and canonical encoding.
3. Verify that every primitive output used as a validation result is constrained.
4. Pin primitive versions, constants, rounds, fields, generated contracts, and transcript domains.
5. Add cross-layer test vectors for circuit, SDK, prover, verifier, and on-chain outputs.
6. Confirm that mocked, placeholder, or keccak-based implementations cannot be used in production paths.
7. For Merkle trees, confirm leaf/internal node separation, zero values, path index convention, and root history behavior.
8. For signatures and curves, confirm range checks, curve checks, subgroup checks, identity handling, and malleability rules.
9. For non-native arithmetic, confirm limb bounds, carries, reductions, comparisons, and canonical outputs.

## Related bugs

- [Insecure Hash Function](../../Proving%20System%20Bugs/Unsecure%20Hash%20Function.md)
- [Lack of Domain Separation](../../Proving%20System%20Bugs/Lack%20of%20Domain%20Seperation.md)
