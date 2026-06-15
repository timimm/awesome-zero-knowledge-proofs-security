# Non-determinism

- [Introduction](#introduction)
- [Cases](#cases)
  - [1. Scroll-zkevm-circuit: is\_zero circuit](#1-scroll-zkevm-circuit-is_zero-circuit)
  - [2. Aztec 2.0: Nondeterministic nullifier](#2-aztec-20-nondeterministic-nullifier)
  - [3. 0xPARC StealthDrop: Nondeterministic nullifier](#3-0xparc-stealthdrop-nondeterministic-nullifier)

## Introduction

Non-determinism means there are multiple valid witnesses or public outputs for the same intended statement. This is especially dangerous for nullifiers, identifiers, state transitions, and accounting logic where uniqueness is part of the security model. In some zkVM-style settings, proof uniqueness is not required, but the application must explicitly tolerate that design.

## Cases

### 1. Scroll-zkevm-circuit: is_zero circuit

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| Scroll | Medium | is_zero circuit | Multiple witness representations may satisfy the same logical zero check if the inverse relation is not fully constrained. |

#### Description

An `is_zero` helper usually proves whether an input is zero by using an inverse witness and a boolean output. If the inverse, input, and output relation is incomplete, the prover may choose alternative witness values that satisfy the surrounding constraints while changing the semantic result of the zero test. Any consumer of such a helper must verify that the output is boolean and that both zero and non-zero branches are constrained.

### 2. Aztec 2.0: Nondeterministic nullifier

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| Aztec Team | High | [Aztec 2.0: Missing Bit Length Check / Nondeterministic Nullifier](https://github.com/0xPARC/zk-bug-tracker#6-aztec-20-missing-bit-length-check--nondeterministic-nullifier) | One note commitment could produce multiple nullifiers, allowing repeated spends. |

#### Description

Aztec notes were spent by publishing a nullifier derived from note-specific data, including the note index. The circuit assumed the index was a 32-bit value but did not constrain that bit length. A prover could use many larger values that shared the same low 32 bits, proving membership for the same note while deriving different nullifiers. The protocol-level uniqueness check therefore failed because the circuit statement allowed several valid nullifier outputs for one spend.

### 3. 0xPARC StealthDrop: Nondeterministic nullifier

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| botdad | High | [0xPARC StealthDrop: Nondeterministic Nullifier](https://github.com/0xPARC/zk-bug-tracker#8-0xparc-stealthdrop-nondeterministic-nullifier) | A claim proof could be generated with different nullifiers for the same entitlement. |

#### Description

The StealthDrop design relied on nullifiers to ensure that each eligible user could claim only once. The bug came from insufficient constraints around the values used to derive the nullifier, so the same logical claim could be represented by more than one valid witness. This case shows that nullifier circuits must constrain not only the final hash output but also the canonical representation of every input used to derive it.
