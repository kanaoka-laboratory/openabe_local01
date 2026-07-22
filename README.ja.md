# OpenABE (オープンエービーイー)

OpenABEは、さまざまな属性ベース暗号（Attribute-Based Encryption, ABE）スキームと業界標準の暗号プリミティブを実装したプロフェッショナルグレードのC++暗号ライブラリです。開発者が暗号学の深い専門知識を持たなくても、柔軟なポリシーベースのアクセス制御を実装できるように設計されています。

## 主な機能
* **柔軟なアクセス制御**: キーポリシーABE (KP-ABE)、暗号文ポリシーABE (CP-ABE)、およびマルチオーソリティABEをサポートしています。
* **高いセキュリティ**: コリュージョン耐性（Collusion-Resistance）および選択暗号文攻撃 (CCA) 耐性を実装しています。
* **包括的なプリミティブ**: 公開鍵暗号 (PKE)、共通鍵暗号 (SKE)、デジタル署名、およびX.509証明書の処理を含んでいます。
* **最適化されたパフォーマンス**: 高効率なペアリング演算のためにBarreto-Naehrig (BN) 曲線を使用しています。

## リポジトリ構成
* `src/`: ライブラリのコアソースコード。
* `include/`: 公開ヘッダーファイル。
* `cli/`: テストおよびデプロイメント用のコマンドラインインターフェースツール。
* `examples/`: ABEの使用例を示すサンプルアプリケーション。
* `bindings/`: 言語バインディング（例：Python）。
* `deps/`: 外部依存関係 (RELIC, OpenSSL, gtest)。
* `docs/`: プロジェクトドキュメント。

## クイックスタート

### インストール
1. システム依存関係をインストールします：
   ```bash
   sudo ./deps/install_pkgs.sh
   ```
2. プロジェクトをビルドします：
   ```bash
   . ./env
   make
   make test
   ```
3. （任意）システムにインストールします：
   ```bash
   sudo make install
   ```

### サンプルの実行
ハイレベルAPIのサンプルをビルドして実行します：
```bash
make examples
cd examples/
./test_cp  # 暗号文ポリシーABE (CP-ABE) の例
./test_kp  # キーポリシーABE (KP-ABE) の例
./test_pk  # 公開鍵暗号 (PKE) の例
```

## ドキュメント
詳細なドキュメントは `docs/` ディレクトリにあります：
* [API仕様書](docs/jp/api_spec.md)
* [設計書](docs/jp/design_doc.md)
* [利用ガイド](docs/jp/usage_guide.md)
* [エラードキュメント](docs/jp/error_docs.md)
* [クラス図](docs/jp/diagrams.md)

## ライセンス
OpenABEはAGPL 3.0ライセンスの下で提供されています。
