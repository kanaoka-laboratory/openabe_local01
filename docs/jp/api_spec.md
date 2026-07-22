# API仕様書

本ドキュメントは、OpenABEの公開APIについて規定します。本ライブラリは、使いやすさを重視したハイレベルラッパーと、詳細な制御が可能なローレベルコンテキストの両方を提供しています。

## グローバルライフサイクル管理
OpenABEの機能を動作させる前に、ライブラリの初期化および終了処理を行う必要があります。

* `void InitializeOpenABE()`: グローバル状態および数学ライブラリを初期化します。アプリケーション起動時に一度だけ呼び出す必要があります。
* `void ShutdownOpenABE()`: グローバルリソースを解放します。アプリケーション終了前に一度だけ呼び出す必要があります。

## ハイレベルAPI (ZCryptoBox)

ハイレベルAPIは、内部的な数学的構造を管理することなくABE操作を行いたい開発者向けに設計されています。

### OpenABECryptoContext
属性ベース暗号（ABE）のための主要クラスです。

#### メソッド:
* `void generateParams()`: 
    * **説明**: スキームに必要なシステムパラメータ（公開パラメータ）を生成します。
    * **戻り値**: なし。失敗した場合は `CryptoException` をスローします。
* `void` `keygen(const std::string& keyInput, const std::string& keyID)`:
    * **説明**: 指定された属性またはポリシーに関連付けられた秘密鍵を生成します。
    * **引数**: 
        * `keyInput`: 属性リスト（例：`"|attr1|attr2|"`）またはアクセスポリシー。
        * `keyID`: 内部キーストアに保存する生成鍵のユニークな識別子。
    * **戻り値**: なし。失敗した場合は `CryptoException` をスローします。
* `void encrypt(const std::string& encInput, const std::string& plaintext, std::string& ciphertext)`:
    * **説明**: ポリシーまたは属性セットに従ってデータを暗号化します。
    * **引数**:
        * `encInput`: ポリシー（例：`"attr1 AND attr2"`）または属性リスト。
        * `plaintext`: 暗号化する元の文字列。
        * `ciphertext`: [出力] 生成された暗号文。
    * **戻り値**: なし。失敗した場合は `CryptoException` をスローします。
* `void decrypt(const std::string& keyID, const std::string& ciphertext, std::string& plaintext)`:
    * **説明**: 指定されたIDで保存されている鍵を使用して暗号文を復号します。
    * **引数**:
        * `keyID`: 使用する秘密鍵の識別子。
        * `ciphertext`: 暗号化されたデータ。
        * `plaintext`: [出力] 復号された平文文字列。
    * **戻り値**: なし。失敗した場合は `CryptoException` をスローします。

### OpenPKEContext
標準的な公開鍵暗号 (PKE) のためのAPIです。

#### メソッド:
* `void keygen()`: 公開鍵と秘密鍵のペアを生成します。
* `void encrypt(const std::string& pubKey, const std::string& plaintext, std::string& ciphertext)`: 提供された公開鍵を使用してデータを暗号化します。
* `void decrypt(const std::string& privKey, const std::string& ciphertext, std::string& plaintext)`: 提供された秘密鍵を使用してデータを復号します。

### OpenPKSIGContext
デジタル署名のためのAPIです。

#### メソッド:
* `void keygen()`: 署名用の鍵ペアを生成します。
* `void sign(const std::string& privKey, const std::string& message, std::string& signature)`: メッセージに署名します。
* `void verify(const std::string& pubKey, const std::string& signature, const std::string& message, bool& isValid)`: 署名を検証します。

## ローレベルAPI (コンテキスト層)

ライブラリを拡張したり、特定のスキームを実装したい上級ユーザー向けです。

### ファクトリ関数
* `OpenABE_createContextABE(const std::string& schemeID)`: 特定のABEスキームコンテキスト（例：`"CP-ABE"`, `"KP-ABE"`）のインスタンスを作成します。
* `OpenABE_createContext SasukeContextPKE(const std::string& schemeID)`: 公開鍵暗号コンテキストを作成します。

## エラー処理
すべてのAPIメソッドは `MessageException` から派生した例外をスローする可能性があります。
ユーザーはAPIコールを `try-catch` ブロックで囲む必要があります：
```cpp
try {
    ctx.encrypt("attr1 AND attr2", pt, ct);
} catch (const CryptoException& e) {
    // 暗号学的エラーの処理
}
```
