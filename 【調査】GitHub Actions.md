# 【調査】GitHub Actions

## 1. GitHub Actionsとは

- **概要**: ソフトウェア開発のワークフローを自動化するための、GitHubに組み込まれたCI/CDプラットフォーム。
- **機能**: コードのプッシュやプルリクエストをトリガーに、テスト、ビルド、デプロイなどの一連の作業を自動化できる。

## 2. 他サービスとの名称の違い

| プラットフォーム | サービス名 | 概要 |
| :--- | :--- | :--- |
| **GitHub** | **GitHub Actions** | 本稿で解説するCI/CDサービス。 |
| **Claude** | (該当なし) | Anthropic社が開発したAIアシスタントであり、CI/CDツールではない。 |
| **Google (GCP)** | **Cloud Build** | GitHub Actionsと同様の機能を持つGoogle Cloud PlatformのCI/CDサービス。 |

**結論**: CI/CDという同じ目的を持つツールでも、提供するプラットフォームによって名称は異なる。

## 3. GitHub ActionsとAIモデルの連携

- **連携方法**:
    - Claude (Anthropic) や Gemini (Google) が提供する**API**を介して連携する。
    - 具体的には、GitHub Actionsのワークフロー（YAMLファイル）から、`curl`などを用いて各AIのAPIを呼び出す。

- **連携手順の概要**:
    1.  **APIキーの取得**: 各AIサービスの提供元からAPIキーを取得する。
    2.  **GitHub Secretsへの登録**: 取得したAPIキーをリポジトリのSecretsに登録し、安全性を確保する。
    3.  **ワークフローの作成**: `.github/workflows/` ディレクトリ配下に、API呼び出し処理を記述したYAMLファイルを作成する。

- **連携による活用例**:
    - **コードレビューの自動化**: プルリクエストの差分をAIに送り、レビューコメントを自動生成させる。
    - **ドキュメントの自動生成**: コードの変更点をAIが解析し、関連ドキュメントを自動で更新する。
    - **コミットメッセージの自動要約**: 変更内容をAIが要約し、統一感のあるコミットメッセージを生成する。

## 参考HP
【最新】GitHub ActionsでGemini CLIを活用してみよう
 https://zenn.dev/makumaaku/articles/15f56ac617a3af
 
