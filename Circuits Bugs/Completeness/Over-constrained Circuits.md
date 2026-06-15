# Over-constrained Circuits

- [Introduction](#introduction)
- [Common patterns](#common-patterns)
- [Cases](#cases)
  - [1. Semaphore: Group IDs outside the SNARK field](#1-semaphore-group-ids-outside-the-snark-field)
  - [2. Zk-Kit: Merkle tree zero values outside the SNARK field](#2-zk-kit-merkle-tree-zero-values-outside-the-snark-field)
- [Review checklist](#review-checklist)

## Introduction

Over-constrained circuits reject valid witnesses because the constraint system is stricter than the intended specification. This breaks completeness: honest users may know valid private data but cannot generate a proof accepted by the verifier.

Completeness bugs are often underestimated because they do not directly let an attacker forge proofs. In deployed systems, however, they can freeze withdrawals, block migrations, or make certain valid states unprovable.

## Common patterns

- Circuit range is narrower than the application range.
- Public input ordering or encoding differs from the contract verifier input.
- Merkle root, zero values, tree depth, or path index convention differs across circuit, SDK, and contract.
- Contract accepts states that the circuit cannot prove.
- Circuit assumes fixed denominations while the application accepts variable amounts.
- Field conversion or non-native arithmetic rejects valid application values.

## Cases

### 1. Semaphore: Group IDs outside the SNARK field

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| PSE Security Team | Medium | [Semaphore: Missing Smart Contract Range Check](https://github.com/0xPARC/zk-bug-tracker#4-semaphore-missing-smart-contract-range-check) | A valid on-chain group could be created in a state that no proof can verify against. |

#### Description

Semaphore allowed a coordinator to create a group with a `uint256` group ID, while the verifier rejected public inputs greater than the SNARK scalar field. If a group ID was valid for the contract but outside the circuit field, members could not produce proofs that passed verification. Although the underlying issue is a missing contract-side range check, the system-level failure is a completeness error because an accepted application state became unprovable.

### 2. Zk-Kit: Merkle tree zero values outside the SNARK field

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| PSE Security Team | Medium | [Zk-Kit: Missing Smart Contract Range Check](https://github.com/0xPARC/zk-bug-tracker#5-zk-kit-missing-smart-contract-range-check) | A Merkle tree could be initialized with values that later make inclusion proofs fail. |

#### Description

The Zk-Kit incremental Merkle tree accepted a `uint256` zero leaf value during initialization. Because the corresponding ZK circuits operate over the SNARK scalar field, a zero value outside that field can make circuit-side hashing and verifier inputs disagree with the on-chain tree. The result is an integration completeness failure: the contract accepts a tree configuration that valid users cannot reliably prove against.

## Review checklist

1. Compare circuit input domains with contract, SDK, and API accepted domains.
2. Generate proofs for boundary values and application-level edge cases.
3. Check that every accepted on-chain state has a corresponding provable circuit statement.
4. Test Merkle proofs across multiple leaves, old roots, empty siblings, and root history rollover.
5. Treat failed proof generation for valid application states as a security issue, not only a UX bug.
