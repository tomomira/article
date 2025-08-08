# 【手順】Python仮想環境 構築手順

Windows環境でPythonの仮想環境を構築する手順です。

### 1. Pythonのバージョン確認
まず、Pythonがインストールされていることを確認します。

```bash
python --version
```

### 2. 仮想環境の作成
プロジェクトのディレクトリで、`venv`という名前の仮想環境を作成します。

```bash
python -m venv venv
```

### 3. 仮想環境のアクティベート
作成した仮想環境を有効にします。実行後、プロンプトの前に `(venv)` と表示されます。

**PowerShellの場合:**
```powershell
.\venv\Scripts\activate
```

**コマンドプロンプトの場合:**
```bash
venv\Scripts\activate
```

### 4. Pythonの実行パス確認
現在有効になっているPythonのパスを確認し、仮想環境内のものが使われていることを確認します。

```bash
where python
```
表示されるパスの中に `venv\Scripts\python.exe` が含まれていることを確認してください。

### 5. インストール済みパッケージの確認
現在の仮想環境にインストールされているパッケージ一覧を表示します。

```bash
pip list
```

### 6. pipのアップグレード
pip自体を最新のバージョンに更新しておきます。

```bash
python -m pip install --upgrade pip
```

以上が基本的な流れです。この後、`pip install <パッケージ名>` を実行することで、この仮想環境内にライブラリを追加していくことができます。
