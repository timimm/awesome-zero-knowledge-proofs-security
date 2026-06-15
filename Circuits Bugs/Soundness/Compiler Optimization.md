# Compiler Optimization

- [Introduction](#introduction)
- [Common patterns](#common-patterns)
- [Review checklist](#review-checklist)
- [Cases](#cases)
  - [1. Halo2: Dynamic advice lookup table overwrite](#1-halo2-dynamic-advice-lookup-table-overwrite)
  - [2. Unused public inputs optimized out](#2-unused-public-inputs-optimized-out)

## Introduction

Compiler optimization bugs happen when the compiler simplifies away constraints or signals in a way that hides the developer's intended security check. This is especially risky when a signal is assigned but not used, a public input is unused, or a validation component output is not connected to a final constraint.

These bugs are hard to catch manually because the source code can look meaningful while the generated constraint system does not enforce the intended relation.

## Common patterns

- Unused public input.
- Unused component output.
- Helper signal computed but not consumed.
- Equality check replaced by assignment.
- Optimized-away boolean or range check.
- Backend-specific optimization assumptions, such as dynamic advice table overwrite issues.

## Review checklist

1. Inspect compiler warnings and fail CI on unused signals or components.
2. Compare source-level intent with generated constraint counts and symbols.
3. Mutate public inputs and witness values to confirm verification fails.
4. Prefer explicit final equality constraints for validation outputs.
5. Review backend-specific optimizer advisories.

## Cases

### 1. Halo2: Dynamic advice lookup table overwrite

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| Halo2 | High | [Dynamic advice lookup table overwrite](https://github.com/zcash/halo2/pull/715) | A lookup table assignment could be overwritten in a way that weakens the intended lookup constraint. |

#### Description

Dynamic advice lookup tables are populated during witness assignment and then used by lookup constraints. If the backend allows a later assignment to overwrite a table cell without preserving the intended relationship, the source circuit may appear to constrain membership while the generated constraint system accepts a different table. This class of bug requires reviewing backend semantics, not only the circuit source.

### 2. Unused public inputs optimized out

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| 0xPARC | High | [Unused Public Inputs Optimized Out](https://github.com/0xPARC/zk-bug-tracker#5-unused-public-inputs-optimized-out) | A verifier may accept proofs even when application-visible public inputs are changed. |

#### Description

Some compilers or proving backends remove signals that do not affect any constraint. If a public input is declared for application use but never enters the constraint system, the proof may not bind that input even though it appears in the source-level interface. Reviewers should mutate each public input after proof generation and inspect the generated constraints or verification key to confirm that the input is actually part of the proved statement.
