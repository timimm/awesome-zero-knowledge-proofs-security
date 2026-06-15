# Assigned but not Constrained

- [Introduction](#introduction)
- [Common patterns](#common-patterns)
- [Cases](#cases)
  - [1. MiMC Hash: Assigned hash output without constraints](#1-mimc-hash-assigned-hash-output-without-constraints)
  - [2. Summa: Constrained comparison result through witness advice](#2-summa-constrained-comparison-result-through-witness-advice)
- [Review checklist](#review-checklist)

## Introduction

Assigned but not constrained bugs happen when a witness value is computed or assigned but is not enforced by the constraint system. In Circom, this often appears through `<--`, unused component outputs, public inputs that never enter any constraint, or helper signals that are only used for readability.

A value being present in the witness or public input list does not mean it is bound by the proof. It must contribute directly or indirectly to a constraint.

## Common patterns

- Public inputs declared for contract execution but never constrained in the circuit.
- Component outputs that represent validation results but are never checked.
- Witness assignments with `<--` followed by missing boolean, range, or reconstruction constraints.
- Helper signals computed for clarity but not used by any final equality.
- Public metadata such as recipient, relayer, fee, chain ID, or verifying contract omitted from the proved statement.

## Cases

### 1. MiMC Hash: Assigned hash output without constraints

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| Kobi Gurkan | High | [MiMC Hash: Assigned but not Constrained](https://github.com/0xPARC/zk-bug-tracker#14-mimc-hash-assigned-but-not-constrained) | A prover can provide witness values that are not forced to equal the intended hash computation. |

#### Description

The circuit used a witness assignment to compute a MiMC hash value, but the assigned output was not fully tied back to constraints. Because the verifier only checks constraints and not the witness generation code, a malicious prover can modify the assigned witness value while still satisfying the remaining constraint system. The vulnerability illustrates that hash computations are only security-relevant when their inputs, rounds, and final output are all constrained.

### 2. Summa: Constrained comparison result through witness advice

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| Summa | High | [Summa: Unconstrained Constants Assignment](https://github.com/0xPARC/zk-bug-tracker#19-summa-unconstrained-constants-assignemnt) | A prover can satisfy a solvency comparison even when the intended inequality is false. |

#### Description

The Summa circuit used a `LessThan` gadget and intended to enforce that the comparison result was equal to one. However, the auxiliary `check` value was assigned during witness generation and then constrained to equal the gadget output, rather than constraining the gadget output directly against a constant. A malicious prover could assign `check` to zero, making the comparison gate consistent even when the intended `lhs < rhs` condition was false.

## Review checklist

1. Mutate each public input after proof generation and verify that proof verification fails.
2. Search for `<--` and confirm every assigned value is later constrained.
3. Check every component output that returns a boolean or validation result.
4. Remove or justify every unused signal and component.
5. Confirm that contract-visible execution parameters are included in constraints or in a constrained statement hash.
