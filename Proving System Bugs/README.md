# Proving System Bugs

A proving system is the cryptographic layer used to prove and verify correct circuit execution. The choice of proving system and framework depends on project requirements and affects the circuit front-end, trusted setup, transcript, verifier, and deployment model.

Unlike circuit bugs, proving-system bugs are usually closer to cryptography, algebra, and protocol implementation. Common classes include:

- [Bad Polynomial Implementation](./Bad%20Polynomial%20Impl.md)
- [Frozen Heart](./Frozen%20Heart.md)
- [Lack of Domain Separation](./Lack%20of%20Domain%20Seperation.md)
- [Missing Curve Point Check](./Missing%20Curve%20Point%20Check.md)
- [Insecure Hash Function](./Unsecure%20Hash%20Function.md)

