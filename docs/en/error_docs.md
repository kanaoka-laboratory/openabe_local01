# Error Documentation

This document describes the error handling mechanism of OpenABE, common errors, and how to troubleshoot them.

## 1. Error Handling Mechanism
OpenABE uses a C++ exception-based system for error reporting. All cryptographic exceptions derive from `MessageException`.

### Exception Hierarchy:
* `std::exception` $\rightarrow$ `MessageException`: Base class providing the `.what()` method.
    * $\rightarrow$ `CryptoException`: Thrown when a general cryptographic operation fails (e.g., invalid parameters, decryption failure).
    * $\rightarrow$ `ZCryptoBoxException`: Specific to errors occurring within the high-level wrapper API.

## 2. Common Error Cases and Causes

| Error / Exception Message | Cause | Possible Solution |
| :--- | :--- | :--- |
| **Decryption Failure** | The private key associated with the `keyID` does not satisfy the access policy of the ciphertext. | Ensure the attributes associated with the secret key meet the requirements of the encryption policy. |
| **Invalid Policy Syntax** | The policy string provided to `encrypt()` or `keygen()` contains syntax errors (e.g., missing parentheses, invalid operators). | Check the boolean logic of the policy strings (use `AND`, `OR` operators correctly). |
| **Key Not Found** | The specified `keyID` was not found in the internal keystore. | Verify that `keygen()` was called with the correct ID before attempting to decrypt. |
| **Initialization Error** | `InitializeOpenABE()` failed or was not called before using the API. | Call `InitializeOpenABE()` at the very beginning of the application startup. |
| **Parameter Mismatch** | The ciphertext was encrypted using parameters from a different setup/system than the one currently used for decryption. | Ensure that both encryptor and decryptor are using the same system parameters (Public Parameters). |
| **Internal Math Error** | A failure occurred in the underlying mathematical library (ZML/RELIC) during pairing or group operations. | This usually indicates a corrupted ciphertext or an unsupported parameter set. |

## 3. Troubleshooting Guide

### Scenario: "Decryption failed" but attributes seem correct
* **Check Formatting**: Ensure that attribute lists are passed as pipe-separated strings (e.g., `"|attr1|attr2|"`) when generating keys.
* **Check Case Sensitivity**: Attributes in OpenABE are case-sensitive. `Attr1` and `attr1` are different attributes.
* **Verify Policy Logic**: Double check if the policy is written as a boolean expression that correctly maps to your logic (e.g., use `(A AND B) OR C`).

### Scenario: Application crashes on startup
* **Check Libraries**: Ensure all dependency libraries (RELIC, OpenSSL) are correctly installed and in the library path (`LD_LIBRARY_PATH`).
* **Verify Initialization**: Confirm that `InitializeOpenABE()` is called before any other API call.

## 4. Best Practices for Error Handling
Wrap all high-level API calls in `try-catch` blocks to prevent application crashes:
```cpp
try {
    ctx.decrypt(keyID, ciphertext, plaintext);
} catch (const CryptoException& e) {
    std::cerr << "Cryptographic error occurred: " << e.what() << std::endl;
    // Perform recovery or notify user
}
```
