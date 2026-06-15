# audit-overview

## Purpose

Use this skill when starting an audit and the first task is to understand the target repository. It produces a structured Chinese-language onboarding overview for either a circuit project or a contract project.

## When to use

Use it for zero-knowledge, cryptographic circuit, or smart-contract repositories where the auditor needs a clear map of source files, core components, critical inputs, constraints, state variables, roles, and security assumptions before writing findings.

## Supported targets

| Target type | Examples | Focus |
|---|---|---|
| Circuit projects | Circom, Cairo, Noir, Halo2, gnark, Arkworks, Plonky2, STARK/PLONK/R1CS-style systems | Public inputs, witnesses, constraints, hash/signature/Merkle usage, setup artifacts, verifier boundaries |
| Contract projects | Solidity, Vyper, Move, Rust/Anchor, verifier adapter contracts | Contract architecture, privileged roles, asset flows, state variables, verifier integration, external dependencies |

## Workflow

1. Detect whether the repository is primarily a circuit project or a contract project.
2. Scan source files while skipping dependencies, build artifacts, generated outputs, and tests unless they clarify expected behavior.
3. Extract the architecture, critical flows, key inputs or state variables, constraints, assumptions, and integration boundaries.
4. Produce `overview.md` in Chinese, starting directly from `## 项目概述`.
5. If requested, generate an HTML overview for visual audit onboarding.

## Circuit review focus

- `template`, `component main`, public signals, private witness, and outputs.
- `===`, `<==`, and `<--` usage.
- Constraint completeness, range checks, boolean constraints, selectors, equality bindings, and public input ordering.
- Hash, signature, Merkle, lookup, table, and gadget usage.
- Setup scripts, proving scripts, verifier export scripts, and generated verifier boundaries.

## Contract review focus

- Contract/module inventory, inheritance, libraries, deployment topology, and external dependencies.
- User-facing and privileged flows with concrete function paths.
- Asset, permission, accounting, replay, root history, upgradeability, and verifier-related state.
- Roles such as owner/admin, relayer, user, token, verifier, bridge, oracle, and governance.

## Output

The output is an audit onboarding document, not a vulnerability report. The attack surface section should remain `TBD` so that later audit passes can fill it with confirmed findings.

