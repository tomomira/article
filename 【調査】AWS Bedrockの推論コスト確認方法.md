# 【調査】AWS Bedrockの推論コスト確認方法

AWS BedrockにおけるClaude 3.5 SonnetなどのLLMの推論コストは、APIのレスポンスに含まれるトークン数とAWSの公式料金表を基に算出できます。

## コストを確認する手順

### 1. 推論APIレスポンスからトークン数を取得する

Bedrockの`InvokeModel` APIを利用して推論を行うと、レスポンスの一部として消費されたトークン数が含まれています。

Python (boto3) を利用する場合、レスポンスボディの中に`usage`というキーで情報が含まれています。

**レスポンスボディの例 (`usage`部分):**
```json
{
    "usage": {
        "input_tokens": 50,
        "output_tokens": 200
    }
}
```
`input_tokens`が入力トークン数、`output_tokens`が出力トークン数です。

### 2. 最新の料金を確認する

AWS公式の料金ページで、利用するモデルの1,000トークンあたりの料金を確認します。料金はモデルやリージョンによって異なるため、常に最新の情報を参照する必要があります。

- [Amazon Bedrock 料金表](https://aws.amazon.com/jp/bedrock/pricing/)

### 3. コストを計算する

取得したトークン数と料金を以下の式に当てはめて、1回の推論あたりのコストを計算します。

**計算式:**
```
(入力トークン数 / 1,000) × 入力料金 + (出力トークン数 / 1,000) × 出力料金
```

---

## プログラムでのトークン数取得について

### Q: 「AIエージェントとは何ですか？」と質問した場合、この一連の動作で利用する入出力のトークン数というイメージですか？

はい、その通りです。
- **入力トークン数:** LLMに送信した質問「AIエージェントとは何ですか？」のテキストのトークン数。
- **出力トークン数:** LLMが生成して返してきた回答テキストのトークン数。

### Q: プログラムで特に明示的にトークン数を取得するコードを書いておかなくても情報は取得できるのでしょうか？

いいえ、**取得できません**。
プログラムでトークン数を取得するコードを明示的に書く必要があります。APIレスポンスにはトークン数情報が含まれていますが、それをプログラムで意図的に読み取る処理を記述しない限り、利用することはできません。

### Pythonでの取得コード例

```python
import boto3
import json

# Bedrockクライアントの作成
bedrock_runtime = boto3.client(service_name='bedrock-runtime')

# Claudeへのリクエストを作成
prompt = "AIエージェントとは何ですか？"
body = json.dumps({
    "anthropic_version": "bedrock-2023-05-31",
    "max_tokens": 1024,
    "messages": [{"role": "user", "content": prompt}]
})

# モデルを呼び出す
response = bedrock_runtime.invoke_model(
    body=body,
    modelId='anthropic.claude-3-5-sonnet-20240620-v1:0'
)

# レスポンスボディを解析
response_body = json.loads(response['body'].read())

# 回答本文を取得
completion = response_body['content'][0]['text']
print(f"回答: {completion}")

# メタデータからトークン数を取得
usage = response_body['usage']
input_tokens = usage['input_tokens']
output_tokens = usage['output_tokens']

print(f"入力トークン数: {input_tokens}")
print(f"出力トークン数: {output_tokens}")
```

## その他のコスト確認方法

- **AWS Cost Explorer:**
  - AWS マネジメントコンソールでBedrockサービス全体の利用料金を視覚的に確認できます。
  - 個別の推論コストのリアルタイムな把握には向きません。
- **コスト配分タグ:**
  - AWS Bedrockでコスト配分タグを設定することで、特定のプロジェクトや部門ごとのコストを追跡・管理することが可能です。

---

##補足: InvokeModelとConverse APIについて

`invoke_model`はBedrockの基本的なAPIですが、より新しく推奨される**Converse API (`converse`)**も存在します。

Converse APIは、異なるLLMを統一された形式で呼び出せるため、モデルの切り替えが容易になり、コードがシンプルになるという利点があります。特別な理由がない限り、新規に開発する場合は**Converse APIの利用が推奨されます**。

### Converse APIでのトークン数取得コード例

```python
import boto3
import json

# Bedrockクライアントの作成
bedrock_runtime = boto3.client(service_name='bedrock-runtime')

# Converse API形式でリクエストを作成
prompt = "AIエージェントとは何ですか？"
messages = [{"role": "user", "content": [{"text": prompt}]}]

# Converse APIを呼び出す
response = bedrock_runtime.converse(
    modelId='anthropic.claude-3-5-sonnet-20240620-v1:0',
    messages=messages,
    inferenceConfig={"maxTokens": 1024}
)

# レスポンスから回答本文を取得
output_message = response['output']['message']
completion = output_message['content'][0]['text']
print(f"回答: {completion}")


# メタデータからトークン数を取得
usage = response['usage']
input_tokens = usage['inputTokens']
output_tokens = usage['outputTokens']
total_tokens = usage['totalTokens']

print(f"入力トークン数: {input_tokens}")
print(f"出力トークン数: {output_tokens}")
print(f"合計トークン数: {total_tokens}")
```
