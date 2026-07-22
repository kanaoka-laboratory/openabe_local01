# Usage Guide

This guide provides step-by-step instructions on how to integrate OpenABE into your C++ applications.

## 1. Basic Setup
Before calling any cryptographic functions, you must initialize the library.

```cpp
#include "openabe/openabe.h"

int main() {
    InitializeOpenABE(); 
    // ... your code here ...
    ShutdownOpenABE();
    return 0;
}
```

## 2. Using Ciphertext-Policy ABE (CP-ABE)
In CP-ABE, the encryptor defines an access policy for the ciphertext.

### Workflow:
1. **Context Initialization**: Create a context specifyng "CP-ABE".
2. **Parameter Generation**: Setup system parameters.
3. **Key Generation**: Generate a key based on user attributes.
4. **Encryption**: Encrypt data with a boolean policy.
5. **Decryption**: Decrypt using the generated key.

### Example:
```cpp
#include "openabe/openabe.h"
#include <iostream>

int main() {
    InitializeOpenABE();

    try {
        // 1. Initialize context for CP-ABE
        OpenABECryptoContext cpabe("CP-ABE");
        cpabe.generateParams();

        // 2. Generate secret key based on attributes: "attr1" and "attr2"
        // Format: pipe-separated strings
        cpabe.keygen("|attr1|attr2|", "my_secret_key");

        // 3. Encrypt data with a policy: "Must have attr1 AND attr2"
        std::string plaintext = "Hello ABE World!";
        std::string ciphertext;
        cpabe.encrypt("attr1 AND attr2", plaintext, ciphertext);
        std::cout << "Encrypted successfully." << std::endl;

        // 4. Decrypt using the key stored as "my_secret_key"
        std::string decryptedText;
        cpabe.decrypt("my_secret_key", ciphertext, decryptedText);
        std::cout << "Decrypted text: " << decryptedText << std::endl;

    } catch (const CryptoException& e) {
        std::cerr << "Error: " << e.what() << std::endl;
    }

    ShutdownOpenABE();
    return 0;
}
```

## 3. Using Key-Policy ABE (KP-ABE)
In KP-ABE, the private key is associated with an access policy and children are attributes in the ciphertext.

### Example:
```cpp
#include "openabe/openabe.h"
#include <iostream>

int main() {
    InitializeOpenABE();

    try {
        OpenABECryptoContext kpabe("KP-ABE");
        kpabe.generateParams();

        // Generate key based on policy: "attr1 OR attr2"
        kpabe.keygen("attr1 OR attr2", "my_policy_key");

        // Encrypt data with attributes: "attr1"
        std::string plaintext = "Sensitive Data";
        std::string ciphertext;
        kpabe.encrypt("|attr1|", plaintext, ciphertext);

        // Decryption will work because the key policy (attr1 OR attr2) is satisfied by attribute 'attr1'
        std::string decryptedText;
        kpabe.decrypt("my_policy_key", ciphertext, decryptedText);
        std::cout << "Decrypted text: " << decryptedText << std::endl;

    } catch (const CryptoException& e) {
        std::cerr << "Error: " << e.what() << std::endl;
    }

    ShutdownOpenABE();
    return 0;
}
```

## 4. Public Key Encryption (PKE)
For standard non-attribute based encryption.

```cpp
OpenPKEContext pke("PKE");
pke.keygen(); // Generates a pair
std::string ct, pt = "Hello", pt2;
pke.encrypt(pubKey, pt, ct);
pke.decrypt(privKey, ct, pt2);
```

## 5. Command Line Interface (CLI) tools
For quick testing without code:
* `oabe_setup`: Setup system parameters.
* `oabe_keygen`: Generate keys from policies/attributes.
* `oabe_enc`: Encrypt files or strings.
* `oabe_dec`: Decrypt encrypted files.
