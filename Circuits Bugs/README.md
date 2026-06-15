# Circuit Bugs

## Introduction

Circuits are the constraint layer of a ZK system. They encode which execution traces are accepted by the verifier and which values remain private.

There are many circuit DSLs and ZK programming systems, such as [Circom](https://github.com/iden3/circom), [Cairo](https://github.com/starkware-libs/cairo), [Noir](https://github.com/noir-lang/noir), [Leo](https://github.com/AleoHQ/leo), [ZoKrates](https://github.com/Zokrates/ZoKrates), [Lurk](https://github.com/lurk-lab/lurk-rs), and [Chiquito](https://github.com/privacy-scaling-explorations/chiquito/). Regardless of syntax, the core security question is whether the intended statement is exactly captured by constraints.

Practical bugs usually fall into three classes: **under-constrained** circuits that break soundness, **over-constrained** circuits that break completeness, and privacy leaks that break zero knowledge.

## Review dimensions

- **Soundness**: Can an invalid statement still produce a valid proof?
- **Completeness**: Can every valid statement still produce a valid proof?
- **Zero knowledge**: Does the proof or public statement leak private witness data?
- **Integration**: Does the proved statement match the SDK, verifier, smart contract, and deployment artifacts?

## Taxonomy

### Soundness

- [General Logic Bug](./Soundness/General%20Logic%20Bug.md)
- [Arithmetic Over/Under Flow](./Soundness/Arithmetic%20Over%20or%20Under%20Flow.md)
- [Mismatched Type or Length](./Soundness/Mismatched%20Type%20or%20Length.md)
- [Non-determinism](./Soundness/Non-determinism.md)
- [Assigned but not Constrained](./Soundness/Assigned%20but%20not%20constrained.md)
- [Cryptographic Primitive Misuse](./Soundness/Cryptographic%20Primitive%20Misuse.md)
- [Compiler Optimization](./Soundness/Compiler%20Optimization.md)
- [Trusted Setup Error](./Soundness/Trusted%20Setup%20Error.md)

### Completeness

- [Over-constrained Circuits](./Completeness/Over-constrained%20Circuits.md)

### Zero knowledge

- [Bad Protocol Design/Implementation](./Zero-Knowledge/Bad%20Protocol%20Design%5CImpl.md)

## High-effort manual review areas

Some bugs are difficult to detect by reading circuit source alone and should be supported by negative tests, witness mutation, public input mutation, generated constraint inspection, or cross-layer test vectors:

- under-constrained public inputs and witness assignments;
- over-constrained edge cases that block valid witnesses;
- finite-field arithmetic and non-native field errors;
- bit decomposition and range-check errors;
- cryptographic primitive misuse, including hash functions, commitments, nullifiers, Merkle proofs, signatures, curve operations, and non-native arithmetic;
- Fiat-Shamir transcript or proof-system configuration errors;
- semantic mismatch between the intended protocol and the proven statement;
- incorrect integration across circuits, SDKs, contracts, and verifier artifacts.
