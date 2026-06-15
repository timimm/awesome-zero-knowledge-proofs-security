# Checklist for Zero Knowledge Circuits

> No security by obscurity.

## Base checks

1. Completeness: Are all constraints correctly defined and covering all input variables?
   - Make sure circuits pass correct computation and reject incorrect computation.
   - Each variable should contribute directly or indirectly to a constraint; otherwise, assigned-but-not-constrained bugs may appear.
2. Input range validation: Do input variables fall within expected ranges to prevent overflow or undefined behaviour?
3. Overflow/underflow check: Do both inputs and operations have adequate range checks?
4. Consistency check: If multiple paths compute the same variable, do they yield the same result?
5. Zero knowledge property check: Does the circuit leak private information?
6. Public input binding: Are all public inputs included in at least one effective constraint?
7. Statement completeness: Does the circuit bind the full statement that the verifier or smart contract will execute?
8. Domain alignment: Do circuit ranges match the accepted ranges in the application layer?

## Integration checks

1. Document all assumptions, especially implicit assumptions.
   - Libraries built for high-performance circuits do not enforce preconditions on inputs.
   - Library integration must not leave under-constrained signals.
2. When circuits integrate with smart contracts, system-level bugs may appear at the boundary.
3. Verifier input order: Do public signals match the verifier ABI and on-chain input array exactly?
4. Artifact provenance: Are circuit source commit, R1CS hash, zkey hash, verification key hash, and verifier bytecode hash recorded?
5. Hash consistency: Do the circuit, SDK, verifier tests, and on-chain contracts use the same hash implementation and parameters?
6. Asset/value binding: If the circuit represents external assets, are token identifiers, denominations, and received amounts bound to the proof statement?
7. Proof system configuration: Are CRS, transcript, curve, public input serialization, and verifier configuration consistent with the intended circuit?

## Architecture design checks

- Double-spending prevention, e.g. if protocol needs nullifier mechanism?
- Front-running.
- Withdrawal intent binding: recipient, relayer, fee, refund/auxiliary data, asset, amount, chain ID, and verifying contract should be bound when relevant.
- Ownership model: Is note ownership based on signatures, public keys, or preimage knowledge, and is this documented consistently?
- Nullifier design: Does the nullifier uniquely bind the spent note and avoid cross-context replay?
- Merkle design: Are leaf format, branch format, zero values, tree depth, path index convention, and root history consistent across circuit, SDK, and contract?

## Circom-specific checks

1. Use `<==` or `===` for constrained assignments; treat `<--` as witness assignment that needs explicit constraints.
2. Do not declare signals or components inside loops; declare arrays outside the loop and index them.
3. Check every component output that represents a validation result.
4. Avoid unused components and signals because they can hide missing constraints.
5. Compile every main circuit and fail CI on warnings or compile errors.
6. Generate a valid proof, mutate each public input one by one, and assert verification failure.
7. Test both valid and invalid witnesses for range checks, Merkle paths, nullifiers, and amount conservation.
8. Pin circomlib/circomlibjs versions and compare circuit, SDK, and on-chain hash outputs.

## Cryptographic primitive misuse checks

1. Hash functions: Are primitive choice, domain separation, input encoding, arity, padding, field conversion, constants, and initial state correct?
2. Commitments: Do commitments bind every required field and provide the intended hiding and binding properties?
3. Nullifiers: Do nullifiers bind the note, context, asset, chain, and application domain needed to prevent replay or double-spending?
4. Merkle proofs: Are leaf format, branch format, zero values, path indices, tree depth, and root history consistent across circuit, SDK, and contract?
5. Signatures: Are public keys, signature ranges, message hashes, malleability rules, and verification outputs fully constrained?
6. Elliptic-curve operations: Are curve membership, subgroup membership, identity points, scalar ranges, and exceptional cases handled?
7. Non-native field arithmetic: Are limb ranges, carries, reductions, comparisons, and canonical outputs fully constrained?
8. Pairings and recursive verifiers: Are curve checks, subgroup checks, pairing equations, transcript inputs, and verifier outputs complete?
9. Fiat-Shamir transcripts: Are challenges domain-separated and bound to all relevant public inputs, commitments, and proof context?
10. Encryption and note delivery: Is encrypted note data consistent with the proved commitment and recipient key assumptions?
11. Cross-layer consistency: Are shared primitives tested with common vectors across circuit, SDK, contract, prover, and verifier tooling?
12. Placeholder implementations: Are mocked, non-cryptographic, or keccak-based placeholders impossible to reach in production paths?

## Hard-to-detect classes

The following bug classes are usually harder to detect manually and should receive explicit test or tooling support:

1. Under-constrained circuits where missing constraints still allow plausible-looking proofs.
2. Over-constrained circuits where valid witnesses are rejected only under edge cases.
3. Arithmetic field errors, including unintended modular reduction and non-native field arithmetic mistakes.
4. Bit decomposition, range-check, overflow, and underflow errors.
5. Incorrect public input handling, unused public inputs, signal aliasing, or verifier input order mismatch.
6. Unsound proof system implementation or configuration.
7. Logic and semantic errors where the circuit proves a different statement from the intended specification.
8. Cryptographic primitive misuse inside circuits and integrations.
9. Fiat-Shamir transcript, challenge derivation, and domain separation errors.
10. Incorrect system integration across circuits, SDKs, contracts, verifiers, and deployment artifacts.

## Circuit Optimizations

1. No redundant constraints: Are there any unnecessary constraints that add computational burden? (Minimum constraint rule)

## Misc

1. Third-party library security:
   - Understanding implicit assumptions: Do not overlook any hidden preconditions required by the library.
   - Avoiding unsafe parameters: Ensure that cryptographic components are initialized with secure and recommended parameters.
2. If customizing a cryptographic scheme, ensure that it has a clear security proof and strictly follows the required implementation guidelines.
3. Code readability: Is the circuit code well-structured and easy to audit?
