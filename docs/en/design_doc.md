# Design Document

This document outlines the architecture and design principles of OpenABE.

## 1. System Architecture
OpenABE is designed using a layered architecture to decouple mathematical primitives from high-level application logic.

### Layered Model
* **Application Layer**: Consists of CLI tools and example applications. This layer interacts only with the High-Level API (ZCryptoBox).
* **Wrapper Layer (ZCryptoBox)**: A facade that simplifies complex operations. It manages key state, handles string-to-object conversions, and provides a simplified interface for end-users.
* **Context Layer**: Implementation of specific cryptographic schemes (e.g., CP-ABE by Waters). This layer handles the actual mathematical logic of encryption and decryption.
* **Math Layer (ZML)**: The foundational library providing elliptic curve operations, bilinear pairings, and group elements. ZML acts as a wrapper around high-performance backends like RELIC or OpenSSL.

## 2. Key Design Patterns

### Strategy Pattern
OpenABE uses the Strategy pattern to support multiple ABE schemes. `OpenABECryptoContext` acts as a client that can switch between different scheme implementations (e.g., Waters CP-ABE vs. GPSW KP-ABE) at runtime based on the provided configuration.

### Facade Pattern
The `ZCryptoBox` API serves as a facade, hiding the complexity of:
* Initializing system parameters.
* Managing complex mathematical objects in memory.
* Handling the mapping between human-readable policies and internal representations.

## 3. Encryption Paradigms Implemented
OpenABE implements three primary ABE models:

### Ciphertext-Policy ABE (CP-ABE)
* **Mechanism**: The ciphertext is associated with an access policy, and private keys are associated with attributes.
* **Control**: The encryptor defines who can decrypt the data based on their traits/roles.

### Key-Policy ABE (KP-ABE)
* **Mechanism**: Attributes are associated with the ciphertext, and the private key contains the access policy.
* **Control**: The key issuer determines what data a user can decrypt based on a specific policy.

### Multi-Authority ABE
* **Mechanism**: Attributes can be issued by multiple independent authorities, preventing a single point of failure or total trust in one entity.

## 4. Security Considerations
* **Collusion Resistance**: OpenABE ensures that users cannot combine their private keys to satisfy a policy that none of them could satisfy individually.
* **CCA Security**: Implements Chosen Ciphertext Attack resistance by adding authentication layers (e.g., AES-GCM) over the ABE KEM, transforming CPA-secure schemes into CCA-secure ones.
* **Thread Safety**: Uses `OpenABEStateContext` to manage per-thread state for underlying math libraries that may not be thread-safe.
