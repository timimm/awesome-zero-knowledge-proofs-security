# Arithmetic Over/Under Flow

- [Introduction](#introduction)
- [Case](#case)

## Introduction

Arithmetic Overflow/Underflow vulnerabilities can occur due to improper handling of arithmetic boundaries and properties in finite fields. Let’s break down this concept and understand why these vulnerabilities happen in zero-knowledge systems.

In zero-knowledge proof systems, calculations are often carried out in finite fields. A finite field has a limited number of elements, typically defined by a large prime number  `p` . All arithmetic operations (addition, multiplication, subtraction, etc.) within these systems are performed modulo  `p`.

In normal operations, if a result exceeds the finite field’s maximum value (i.e., is greater than or equal to p), the operation result is reduced by p, effectively wrapping around. This ensures that results always remain within the range [0, p-1]. This behavior avoids traditional overflow (in the classical computing sense), as the result is always within the bounds of the field.

As a result, overflow and underflow do not trigger errors but rather "wrap around". If developers fail to account for this behavior, it can lead to issues such as input alias attacks.

- Input Alias Attack
  
  In ZK systems, constraints are used to define valid inputs and operations, which is on a finite field by default. But if inputs are not constrained correctly or if operations are allowed to exceed the modulus without adequate checks, overflow vulnerabilities can arise. This is known as the input alias attack.
  

## Case

### 1. Dark Forest v0.3: Missing bit length check

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| Daira Hopwood | High | [Dark Forest v0.3: Missing Bit Length Check](https://github.com/0xPARC/zk-bug-tracker#1-dark-forest-v03-missing-bit-length-check) | A range proof could be satisfied by values outside the intended bounds. |

#### Description

Dark Forest used a `RangeProof` circuit built on CircomLib's `LessThan` comparator. The comparator assumed its inputs fit within the configured bit width, but the circuit did not separately constrain the bit length of those inputs. A prover could provide larger field elements that wrapped under the comparator's internal representation, causing the range proof to accept values outside the intended domain.

### 2. EY Nightfall: Nullifier overflow across EVM and SNARK fields

| Identifier | Severity | Location | Impact |
| :--------: | :------: | :------: | :----: |
| BlockHeader | High | [EY Nightfall: Missing Nullifier Range Check](https://github.com/0xPARC/zk-bug-tracker#18-ey-nightfall-missing-nullifier-range-check) | A spent note could be represented by multiple on-chain nullifier values. |

#### Description

Nightfall stored nullifiers as EVM `uint256` values, while the circuit interpreted public inputs modulo the SNARK scalar field. Without an on-chain range check, a user could submit `n` and later submit `n + p`, where `p` is the SNARK field order. The circuit reduced both values to the same field element, but the contract treated them as different nullifiers, enabling a repeated spend.
