# 利用ガイド

このガイドでは、OpenABEをC++アプリケーションに統合するためのステップバイステップの手順を説明します。

## 1. 基本的なセットアップ
暗号化関数を呼び出す前に、ライブラリを初期化する必要があります。

```cpp
#include "openabe/openabe.h"

int main() {
    InitializeOpenABE(); 
    // ... ここにコードを記述 ...
    ShutdownOpenABE();
    return 0;
}
```

## 2. 暗号文ポリシーABE (CP-ABE) の利用
CP-ABEでは、暗号化者が暗号文に対するアクセスポリシーを定義します。

### ワークフロー:
1. **コンテキストの初期化**: "CP-ABE"を指定してコンテキストを作成します。
2. **パラメータ生成**: システムパラメータをセットアップします。
3. **鍵生成**: ユーザー属性に基づいて鍵を生成します。
4. **暗号化**: ブールポリシーを使用してデータを暗号化します。
5. **復号**: 生成した鍵を使用して復号します。

### 例:
```cpp
#include "openabe/openabe.h"
#include <iostream>

int main() {
    InitializeOpenABE();

    try {
        // 1. CP-ABE用のコンテキストを初期化
        OpenABECryptoContext cpabe("CP-ABE");
        cpabe.generateParams();

        // 2. 属性 "attr1" と "attr2" に基づいて秘密鍵を生成
        // 形式: パイプ区切りの文字列
        cpabe.keygen("|attr1|attr2|", "my_secret_key");

        // 3. ポリシー "attr1 AND attr2 であること" でデータを暗号化
        std::string plaintext = "Hello ABE World!";
        std::string ciphertext;
        cpabe.encrypt("attr1 AND attr2", plaintext, ciphertext);
        std::cout << "暗号化に成功しました。" << std::endl;

        // 4. "my_secret_key" として保存された鍵を使用して復号
        std::string decryptedText;
        cpabe.decrypt("my_secret_key", ciphertext, decryptedText);
        std::cout << "復号されたテキスト: " << decryptedText << std::endl;

    } catch (const CryptoException& e) {
        std::cerr << "エラー: " << e.what() << std::endl;
    }

    ShutdownOpenABE();
    return 0;
}
```

## 3. キーポリシーABE (KP-ABE) の利用
KP-ABEでは、秘密鍵にアクセスポリシーが紐付けられ、暗号文には属性が含まれます。

### 例:
```cpp
#include "openabe/openabe.h"
#include <iostream>

int main() {
    InitializeOpenABE();

    try {
        OpenABECryptoContext kpabe("KP-ABE");
        kpabe.generateParams();

        // ポリシー "attr1 OR attr2" に基づいて鍵を生成
        kpabe.keygen("attr1 OR attr2", "my_policy_key");

        // 属性 "attr1" を指定してデータを暗号化
        std::string plaintext = "Sensitive Data";
        std::string ciphertext;
        kpabe.encrypt("|attr1|", plaintext, ciphertext);

        // キーポリシー (attr1 OR attr2) が属性 'attr1' によって満たされるため、復号は成功します
        std::string decryptedText;
        kpabe.decrypt("my_policy_key", ciphertext, decryptedText);
        std::cout << "復号されたテキスト: " << decryptedText << std::endl;

    } catch (const CryptoException& e) {
        std::cerr << "エラー: " << e.what() << std::endl;
    }

    ShutdownOpenABE();
    return 0;
}
```

## 4. 公開鍵暗号 (PKE)
属性ベースではない標準的な暗号化です。

```cpp
OpenPKEContext pke("PKE");
pke.keygen(); // ペアを生成
std::string ct, pt = "Hello", pt2;
pke.encrypt(pubKey, pt, ct);
pke.decrypt(privKey, ct, pt2);
```

## 5. コマンドラインインターフェース (CLI) ツール
コードを書かずにクイックにテストする場合：
* `oabe_setup`: システムパラメータをセットアップします。
* `oabe_keygen`: ポリシー/属性から鍵を生成します。
* `oabe_enc`: ファイルまたは文字列を暗号化します。
* `oabe_dec`: 暗号文ファイルを復号します。
