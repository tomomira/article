# 【調査】GitHub Actions 最小準備メモ

GitHub Actionsの最小構成を試し、コードがプッシュされた際に自動でPythonコードのフォーマットチェックが実行されるまでを確認するための手順メモ。

## 概要

1.  わざとフォーマットを崩したPythonファイル (`sample.py`) を準備する。
2.  Pythonのコードフォーマッター `black` を使ってチェックを行うためのワークフローファイル (`python-formatter-check.yml`) を準備する。
3.  これらのファイルをGitHubリポジトリにプッシュする。
4.  GitHubの「Actions」タブで、ワークフローが自動実行され、フォーマットエラーで失敗することを確認する。

---

## 1. 準備するファイル

ローカルのプロジェクトフォルダに、以下の2つのファイルを作成します。

### a. Pythonサンプルプログラム (`sample.py`)

ルートディレクトリに作成します。インデントやスペースがわざと不揃いになっています。

```python
# sample.py
import    os

def  my_function( name ):
    print( "Hello, " + name)
    if name == "World":
      return True
    else:
        return  False

my_function(   "World"   )

```

### b. GitHub Actions ワークフローファイル

プロジェクト内に `.github/workflows/` というディレクトリを作成し、その中に以下のYAMLファイルを作成します。

**ファイルパス:** `.github/workflows/python-formatter-check.yml`

```yaml
# .github/workflows/python-formatter-check.yml

# ワークフローの名前
name: Python Black Formatter Check

# ワークフローが実行されるきっかけ (mainブランチへのpush)
on:
  push:
    branches:
      - main

# 実行する一連の処理
jobs:
  check-format:
    # 実行する仮想マシンのOS
    runs-on: ubuntu-latest

    # 処理のステップ
    steps:
      # 1. リポジトリのコードを仮想マシンにコピーする
      - name: Checkout code
        uses: actions/checkout@v4

      # 2. Python環境をセットアップする
      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.10'

      # 3. コードフォーマッター(black)をインストールする
      - name: Install black
        run: pip install black

      # 4. blackを実行して、フォーマットが正しいかチェックする
      #    --check オプションは、ファイルを変更せずにフォーマット違反がないかを確認するモード
      - name: Check code format with black
        run: black --check .

```

---

## 2. GitHubリポジトリへのプッシュ手順

1.  **Gitリポジトリの初期化**
    ```bash
    git init
    git branch -M main
    ```

2.  **GitHubで新しいリモートリポジトリを作成**
    （リポジトリ名は任意、`public` で作成）
    作成後、表示されるURLをコピーしておきます。（例: `https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git`）

3.  **リモートリポジトリの追加**
    ```bash
    git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
    ```

4.  **ファイルをステージングしてコミット**
    ```bash
    git add .
    git commit -m "Initial commit with sample code and GitHub Actions workflow"
    ```

5.  **プッシュ**
    ```bash
    git push -u origin main
    ```

---

## 3. GitHub上での動作確認

1.  プッシュが完了したら、ブラウザでGitHubリポジトリのページを開きます。
2.  上部にある「**Actions**」タブをクリックします。
3.  「Python Black Formatter Check」というワークフローが自動的に実行されているのが確認できます。
4.  コードのフォーマットが崩れているため、このワークフローは「`check-format`」ステップで**失敗**するはずです。

これで、GitHub Actionsが正しく設定され、コードのプッシュをきっかけに自動で処理が実行されることを確認できます。

---

## 【補足】Pythonコードフォーマッター `black` とは

`black`は、Python専用の**自動コードフォーマッター**です。

一言で言うと、**「コーディングスタイル（コードの見た目）を、問答無用でたった一つの綺麗な形式に統一してくれるツール」** です。

#### `black`の最大の特徴：「妥協しない」こと

`black`は「The Uncompromising Code Formatter（妥協しないコードフォーマッター）」というキャッチフレーズで知られています。
他の多くのフォーマッターと違い、細かな整形ルールを設定するオプションがほとんどありません。これにより、以下のようなメリットが生まれます。

1.  **スタイルに関する議論がなくなる**:
    「ここのインデントは？」「1行の長さは？」といった、機能とは関係のない見た目に関する議論やレビューでの指摘が一切不要になります。`black`が絶対的なルールだからです。

2.  **誰が書いても同じコードになる**:
    チーム全員が`black`を使うことで、書いた人によらず、プロジェクト全体のコードが一貫したスタイルに保たれます。これにより、コードが非常に読みやすくなります。

3.  **自動化しやすい**:
    設定が不要なため、GitHub ActionsのようなCI/CDツールに組み込み、コードがプッシュされるたびに自動でチェックや整形を行うことが非常に簡単です。

#### 具体的な整形例

例えば、`sample.py`に`black`を適用すると、以下のようになります。

**整形前 (Before)**
```python
# sample.py
import    os

def  my_function( name ):
    print( "Hello, " + name)
    if name == "World":
      return True
    else:
        return  False

my_function(   "World"   )
```

**`black`で整形後 (After)**
```python
# sample.py
import os


def my_function(name):
    print("Hello, " + name)
    if name == "World":
        return True
    else:
        return False


my_function("World")
```
余分なスペースや空行が整理され、文字列がダブルクォーテーション(`"`)に統一されるなど、細部が一貫したルールで整形されているのがわかります。

#### 使い方
*   **インストール**:
    ```bash
    pip install black
    ```
*   **フォーマットチェックのみ行う (ファイルは変更しない)**:
    ```bash
    black --check .
    ```
    (GitHub Actionsで使ったコマンドです)
*   **ファイルを実際に整形する**:
    ```bash
    black .
    ```

`black`は、Python開発における「コードの見た目を整える」という作業を完全に自動化し、開発者がより本質的なロジックの実装に集中できるように手助けしてくれる、非常に人気の高いツールです。

---

## 【補足】GitHub Actionsの便利なアイデア集

コードチェックのような、比較的手軽に始められて開発が便利になるGitHub Actionsのアイデアを、目的別にいくつかご紹介します。

### カテゴリ1：コードの品質と一貫性を保つ

#### 1. Linterによる静的解析
*   **何をするか**：コードの文法エラー、明らかなバグ、スタイルガイド違反などを自動で検出します。フォーマッターよりも一歩踏み込んだチェックです。
*   **なぜ便利か**：単純なミスや「良くない書き方」をコードがマージされる前に発見できるため、コード全体の品質が向上します。
*   **具体的なツール例**：
    *   JavaScript: `ESLint`
    *   Python: `flake8`, `ruff`
    *   Ruby: `RuboCop`

#### 2. Secret（機密情報）のスキャン
*   **何をするか**：コード内に誤ってコミットされたAPIキーやパスワードなどの機密情報がないかスキャンします。
*   **なぜ便利か**：機密情報がリポジトリに記録されるのを防ぎ、深刻なセキュリティ事故を未然に防止できます。
*   **具体的なツール例**：
    *   `git-secrets`, `TruffleHog`
    *   GitHub自身もリポジトリに対して自動で[Secret Scanning](https://docs.github.com/ja/code-security/secret-scanning/about-secret-scanning)機能を提供しています。

#### 3. 依存ライブラリの脆弱性チェック
*   **何をするか**：`package.json` (Node.js) や `requirements.txt` (Python) などで定義された、利用しているライブラリに既知の脆弱性がないかチェックします。
*   **なぜ便利か**：自分たちが書いていないコード（ライブラリ）が原因で発生するセキュリティリスクを早期に発見し、対処できます。
*   **具体的なツール例**：
    *   `npm audit` や `yarn audit`
    *   GitHubの `Dependabot` がこの機能を自動で提供しています。

---

### カテゴリ2：テストの自動実行

#### 4. ユニットテストの実行
*   **何をするか**：コードをプッシュするたびに、あらかじめ書かれたユニットテストをすべて実行します。
*   **なぜ便利か**：新しい変更によって既存の機能が壊れていないか（デグレードしていないか）を常に確認でき、安心してコードを修正・追加できます。
*   **具体的なツール例**：
    *   Python: `pytest`
    *   JavaScript: `Jest`, `Vitest`
    *   Java: `JUnit`

#### 5. テストカバレッジのレポート
*   **何をするか**：テストがコード全体の何パーセントをカバーしているか（網羅しているか）を計測し、結果をレポートします。
*   **なぜ便利か**：テストが不足している箇所が一目でわかり、テストを書く文化をチームに根付かせるきっかけになります。
*   **具体的なツール例**：
    *   `Codecov` や `Coveralls` といった外部サービスと連携させることが多いです。

---

### カテゴリ3：ビルドとデプロイの自動化

#### 6. Dockerイメージのビルドとプッシュ
*   **何をするか**：アプリケーションをコンテナ化するためのDockerイメージを自動でビルドし、Docker HubやGCR (Google Container Registry) などのレジストリにプッシュします。
*   **なぜ便利か**：手元でビルドする手間が省け、誰がやっても同じ実行環境を確実に作成できます。
*   **具体的なツール例**：
    *   公式の`docker/build-push-action`がよく使われます。

#### 7. 静的サイトの自動デプロイ
*   **何をするか**：Jekyll, Hugo, Next.jsなどで作られた静적サイト（ブログやドキュメントサイトなど）をビルドし、GitHub PagesやNetlify, Vercelなどに自動でデプロイします。
*   **なぜ便利か**：`main`ブランチにマージするだけで、数分後にはサイトが自動で更新されるため、コンテンツの公開が非常に楽になります。
*   **具体的なツール例**：
    *   `actions/deploy-pages` (GitHub Pages用)

---

### カテゴリ4：通知とリポジトリ管理

#### 8. SlackやMicrosoft Teamsへの通知
*   **何をするか**：デプロイが成功・失敗した時や、重要なプルリクエストが作成された時などに、チームのチャットツールに自動で通知を送ります。
*   **なぜ便利か**：チームメンバーがGitHubを常に監視していなくても、重要なイベントにすぐに気づくことができます。
*   **具体的なツール例**：
    *   `slackapi/slack-github-action` など、マーケットプレイスに多数公開されています。

#### 9. IssueやPull Requestへの自動ラベル付け
*   **何をするか**：作成されたIssueやPull Requestのタイトルや本文、変更されたファイルパスなどに基づいて、自動で`bug`や`documentation`といったラベルを付けます。
*   **なぜ便利か**：Issueの整理やトリアージ（優先順位付け）の手間を削減できます。
*   **具体的なツール例**：
    *   `actions/labeler`

#### 10. 定期実行（Cron）
*   **何をするか**：「毎日深夜0時に実行」「毎週月曜の朝9時に実行」のように、スケジュールを組んでワークフローを定期的に実行します。
*   **なぜ便利か**：データの定期的なバックアップ、外部APIから最新の情報を取得してリポジトリを更新する、といったバッチ処理を自動化できます。
*   **設定方法**：
    *   ワークフローのトリガーで `on: schedule:` を使います。

これらのアイデアはほんの一部です。GitHub Marketplaceには世界中の開発者が作成した便利なActionが数多く公開されているため、組み合わせることで様々な自動化が実現できます。

