# Trusted Setup Error

## Introduction

Trusted setup is a critical initialization phase in zkp systems, essential for generating Common Reference String (CRS) used both in proof generation and verification without relying on any single party. Essentially, the trusted setup process encodes the circuit's mathematical structure into a set of public parameters.

The security of this process hinges on two fundamental assumptions: 
- the participants must honestly discard the toxic waste.
- the generated parameters must maintain algebraic independence from another.

When these assumptions break, attackers can exploit the resulting linear relationship to forge proof, undermining the soundness of the system and allowing the malicious actors to circumvent the verification.

  - [Vitalik: How do trusted setups work?](https://vitalik.eth.limo/general/2022/03/14/trustedsetup.html)
  - [setup-ceremony](https://zkproof.org/2021/06/30/setup-ceremonies/)
## Cases
### 1. Foomcash incident
  - [FoomCash incident](https://github.com/timimm/Attack-PoC#foomcash---zk-verifier-misconfiguration)