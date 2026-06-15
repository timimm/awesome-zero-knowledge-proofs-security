# Bad Protocol Design/Impl

## Introduction

Zero knowledge errors happen when a proof reveals private witness information or when the protocol omits the blinding and hiding mechanisms required by the proving system. These bugs may leave soundness intact while still defeating the privacy goal of the application.

## Cases

### 1. Dusk Network: Missing blinding factors

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| Dusk Network Team | High | [Dusk Network: Missing Blinding Factors](https://github.com/0xPARC/zk-bug-tracker#17-dusk-network-missing-blinding-factors) | Proof data could leak private witness information. |

#### Description

Dusk's original Plonk implementation omitted some blinding factors that are required to make the protocol zero knowledge. The proving system could still produce succinct proofs, but the proof objects did not sufficiently hide the witness polynomials. This case shows that implementing only the algebraic verification relation is not enough; the privacy-preserving randomization steps are part of the security-critical protocol.

## References

- [Dusk Network Plonk issue](https://github.com/dusk-network/plonk/issues/650)
