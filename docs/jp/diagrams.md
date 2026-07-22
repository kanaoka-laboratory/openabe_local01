# クラス図

このドキュメントでは、Mermaidダイアグラムを使用してOpenABEの構造的な概要を示します。

## ハイレベルAPIアーキテクチャ
以下のダイアグラムは、`ZCryptoBox` ハイレベルラッパーが基底にあるスキームコンテキストとどのように相互作用するかを示しています。

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

## レイヤー依存関係モデル
このダイアグラムは、アプリケーションレベルから数学的プリミティブまでの依存関係の流れを示しています。

```mermaid
graph TD
    subgraph ApplicationLayer [アプリケーション層]
        CLI[CLIツール]
        Examples[サンプルアプリ]
    end

    subgraph WrapperLayer [ラッパー層 - ZCryptoBox]
        HighAPI[ハイレベルAPIクラス]
    end

    subgraph ContextLayer [コンテキスト層]
        ABEContexts[ABEスキームコンテキスト]
        PKEContexts[PKEコンテキスト]
        SKEContexts[SKEコンテキスト]
    end

    subgraph MathLayer [数学層 - ZML]
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

## ABE ワークフローシーケンス
典型的なABEの暗号化/復号の流れです。

```mermaid
sequenceDiagram
    participant User as 開発者
    participant API as OpenABECryptoContext
    participant Context as SchemeContext (例: CP-ABE)
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
    
    User->>API: decrypt("key0", ct, pt2)
    API->>Store: Retrieve Key ("key0")
    Store-->>API: SecretKey
    API->>Context: decrypt(SecretKey, ciphertext)
    Context-->>API: plaintext
```
