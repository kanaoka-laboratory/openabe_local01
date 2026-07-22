# Class Diagrams

This document provides a structural overview of OpenABE using Mermaid diagrams.

## High-Level API Architecture
The following diagram shows how the `ZCryptoBox` high-level wrapper interacts with the underlying scheme contexts.

```mermaid
classDiagram
    class ZCryptoBox {
        <<interface>>
        +InitializeOpenABE()
        +ShutdownOpenABE()
    }

    class OpenABECryptoContext {
        +generateParams()
        +keygen(keyInput, keyID)
        +encrypt(encInput, plaintext, ciphertext)
        +decrypt(keyID, ciphertext, plaintext)
    }

    class OpenPKEContext {
        +keygen()
        +encrypt(pubKey, pt, ct)
        +decrypt(privKey, ct, pt)
    }

    class OpenPKSIGContext {
        +keygen()
        +sign(privKey, msg, sig)
        +verify(pubKey, sig, msg, isValid)
    }

    ZCryptoBox <|-- OpenABECryptoContext
    ZCryptoBox <|-- OpenPKEContext
    ZCryptoBox <|-- OpenPKSIGContext
```

## Layered Dependency Model
This diagram illustrates the dependency flow from the application level down to the mathematical primitives.

```mermaid
graph TD
    subgraph ApplicationLayer [Application Layer]
        CLI[CLI Tools]
        Examples[Example Apps]
    end

    subgraph WrapperLayer [Wrapper Layer - ZCryptoBox]
        HighAPI[High-Level API Classes]
    end

    subgraph ContextLayer [Context Layer]
        ABEContexts[ABE Scheme Contexts]
        PKEContexts[PKE Contexts]
        SKEContexts[SKE Contexts]
    end

    subgraph MathLayer [Math Layer - ZML]
        ZGroup[ZGroup / ZElement]
        ZPairing[ZPairing]
        Backend[RELIC / OpenSSL]
    end

    CLI --> HighAPI
    Examples --> HighAPI
    HighAPI --> ABEContexts
    HighAPI --> PKEContexts
    HighAPI --> SKEContexts
    ABEContexts --> ZGroup
    PKEContexts --> ZGroup
    SKEContexts --> ZGroup
    ZGroup --> Backend
    ZPairing --> Backend
```

## ABE Workflow Sequence
A typical ABE encryption/decryption flow.

```mermaid
sequenceDiagram
    participant User as Developer
    participant API as OpenABECryptoContext
    participant Context as SchemeContext (e.g. CP-ABE)
    participant Store as KeyStore

    User->>API: generateParams()
    API->>Context: setup()
    
    User->>API: keygen("|attr1|attr2|", "key0")
    API->>Context: extractSecretKey(attributes)
    Context-->>Store: Save Key ("key0")

    User->>API: encrypt("attr1 AND attr2", pt, ct)
    API->>Context: encrypt(policy, plaintext)
    Context-->>API: ciphertext
    
    User->>API: decrypt("key0", ct, pt2)
    API->>Store: Retrieve Key ("key0")
    Store-->>API: SecretKey
    API->>Context: decrypt(SecretKey, ciphertext)
    Context-->>API: plaintext
```
