# API Specification

This document specifies the public API of OpenABE. The library provides both high-level wrappers for ease of use and low-level contexts for fine-grained control.

## Global Lifecycle Management
Before using any OpenABE functionality, the library must be initialized and eventually shut down.

* `void InitializeOpenABE()`: Initializes global state and math libraries. Must be called once at application startup.
* `void ShutdownOpenABE()`: Cleans up global resources. Must be called once before application exit.

## High-Level API (ZCryptoBox)

The high-level API is designed for developers who want to perform ABE operations without managing internal mathematical structures.

### OpenABECryptoContext
The primary class for Attribute-Based Encryption.

#### Methods:
* `void generateParams()`: 
    * **Description**: Generates the system parameters (Public Parameters) required for the scheme.
    * **Returns**: Nothing. Throws `CryptoException` on failure.
* `void keygen(const std::string& keyInput, const std::string& keyID)`:
    * **Description**: Generates a private secret key associated with the given attributes or policy.
    * **Parameters**: 
        * `keyInput`: Attribute list (e.g., `"|attr1|attr2|"`) or Access Policy.
        * `keyID`: Unique identifier to associate with the generated key in the internal keystore.
    * **Returns**: Nothing. Throws `CryptoException` on failure.
* `void encrypt(const std::string& encInput, const std::string& plaintext, std::string& ciphertext)`:
    * **Description**: Encrypts data according to a policy or attribute set.
    * **Parameters**:
        * `encInput`: Policy (e.g., `"attr1 AND attr2"`) or Attribute list.
        * `plaintext`: The raw string to encrypt.
        * `ciphertext`: [Out] The resulting encrypted ciphertext.
    * **Returns**: Nothing. Throws `CryptoException` on failure.
* `void decrypt(const std::string& keyID, const std::string& ciphertext, std::string& plaintext)`:
    * **Description**: Decrypts the ciphertext using a key stored under the specified ID.
    * **Parameters**:
        * `keyID`: The identifier of the private key to use.
        * `ciphertext`: The encrypted data.
        * `plaintext`: [Out] The decrypted plaintext string.
    * **Returns**: Nothing. Throws `CryptoException` on failure.

### OpenPKEContext
API for standard Public Key Encryption.

#### Methods:
* `void keygen()`: Generates a public/private key pair.
* `void encrypt(const std::string& pubKey, const std::string& plaintext, std::string& ciphertext)`: Encrypts data using the provided public key.
* `void decrypt(const std::string& privKey, const std::string& ciphertext, std::string& plaintext)`: Decrypts data using the provided private key.

### OpenPKSIGContext
API for digital signatures.

#### Methods:
* `void keygen()`: Generates a signature key pair.
* `void sign(const std::string& privKey, const std::string& message, std::string& signature)`: Signs a message.
* `void verify(const std::string& pubKey, const std::string& signature, const std::string& message, bool& isValid)`: Verifies a signature.

## Low-Level API (Context Layer)

For advanced users who need to extend the library or implement specific schemes.

### Factory Functions
* `OpenABE_createContextABE(const std::string& schemeID)`: Creates an instance of a specific ABE scheme context (e.g., `"CP-ABE"`, `"KP-ABE"`).
* `OpenABE_createContextPKE(const std::string& schemeID)`: Creates a public key encryption context.

## Error Handling
All API methods may throw exceptions derived from `MessageException`. 
Users should wrap API calls in `try-catch` blocks:
```cpp
try {
    ctx.encrypt("attr1 AND attr2", pt, ct);
} catch (const CryptoException& e) {
    // Handle cryptographic error
}
```
