---
marp: true
theme: default
paginate: true
header: 'RAGAS 評価導入方法'
footer: 'Generated from ChatGPT conversation'
---

# RAGAS 評価導入方法

生成AIとRAGシステムの評価フレームワーク

---

## 概要

**RAGAS（Retrieval-Augmented Generation Assessment Suite）とは**

- RAGシステムを評価するPythonベースのツール
- 質問応答、要約、情報検索などの性能を定量的に評価
- 生成AIの信頼性を客観的に測定

**やりたいこと**
1. 生成AIに質問する（分析・要約）
2. RAGASで評価してアウトプット
3. 評価結果をテキスト形式で出力

---

## 前提条件

- **Python 3.8 以上**
- 基本的なPython環境（pip、仮想環境など）

---

## 導入方法

### 1. 仮想環境作成（推奨）
```bash
python -m venv ragas-env
source ragas-env/bin/activate  # Windows: ragas-env\Scripts\activate
```

### 2. RAGASインストール
```bash
pip install ragas
# 評価に必要なモデルも含める場合
pip install ragas transformers sentence-transformers
```

---

## RAGASの基本構造

RAGASは以下の構造のデータセットが必要：

```python
{
    "question": ["質問文"],
    "contexts": [["参照文書1", "参照文書2"]],
    "answer": ["生成AIの回答"],
    "ground_truth": ["正解（オプション）"]
}
```

---

## 評価メトリクス

| メトリクス | 説明 | Ground Truth必要 |
|-----------|------|------------------|
| **faithfulness** | 回答が参照文書に忠実か | ❌ |
| **answer_relevancy** | 回答が質問に関連しているか | ❌ |
| **context_precision** | 適切な文脈を使用しているか | ❌ |
| **context_recall** | 必要な情報が文脈に含まれているか | △ |

---

## 基本的な評価フロー

### ① 生成AIに質問
```python
def ask_ai(question):
    # RAGシステムで回答と参照文書を取得
    answer = "生成AIの回答"
    contexts = ["参照文書1", "参照文書2"]
    return answer, contexts
```

### ② RAGASで評価
```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy

results = evaluate(dataset, metrics=[faithfulness, answer_relevancy])
```

---

## 実装例

```python
from datasets import Dataset
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_precision

# データ準備
data = Dataset.from_dict({
    "question": ["質問内容"],
    "contexts": [["参照文書1", "参照文書2"]],
    "answer": ["生成AIの回答"]
})

# 評価実行
results = evaluate(data, metrics=[faithfulness, answer_relevancy, context_precision])

# 結果出力
for metric, score in results.items():
    print(f"{metric.name}: {score:.4f}")
```

---

## よくある質問と回答

**Q1: ①〜➁は一つのスクリプトでできますか？**
→ **A: はい、完全に可能です**

**Q2: Ground Truthが分からない場合は？**
→ **A: 一部メトリクスでは不要。faithfulness等で評価可能**

**Q3: インストール容量は大きいですか？**
→ **A: モデル込みで約1GB（初回のみ）**

---

## AWS Lambdaでの対応

### 制約と対応策

| 方法 | 制限 | 対応可能性 |
|------|------|------------|
| **Lambda（zip）** | 250MB | ❌ 厳しい |
| **Lambda（コンテナ）** | 10GB | ✅ 対応可能 |
| **SageMaker/ECS** | 制限なし | ✅◎ 最適 |

### 推奨構成
- Lambdaコンテナ化 + 軽量モデル
- 非同期評価での運用

---

## Ground Truth不要の評価

**適用場面**
- 分析・要約タスク
- 正解が事前に存在しないケース
- 信頼性の客観的評価が必要

**評価可能なメトリクス**
- `faithfulness`: 根拠に忠実か
- `answer_relevancy`: 質問に合っているか
- `context_precision`: 余計な情報がないか

---

## テンプレート例
<style scoped>
section {
  font-size: 1.8em;
}

</style>
```python
# Ground Truth不要の評価テンプレート
from datasets import Dataset
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_precision

# データ準備
question = "このレポートの売上推移から重要な傾向を分析してください。"
contexts = ["2023年Q1の売上は前年同期比で12%増加...", "Q2ではアジア市場で..."]
answer = "全体として前年比で売上は増加傾向にあり..."

data = Dataset.from_dict({
    "question": [question],
    "contexts": [contexts], 
    "answer": [answer]
})

# 評価実行
results = evaluate(data, metrics=[faithfulness, answer_relevancy, context_precision])
```

---

## 評価に向かないパターン

### ❌ 使用困難なケース

1. **Contextなしの通常のLLM回答**
   - RAGシステム限定のため

2. **創造的な出力**
   - ストーリー生成、詩、広告文案など

3. **推論・価値判断を伴う回答**
   - 主観的な評価が必要

4. **Contextの品質が悪い場合**
   - 誤情報でもfaithfulnessは高く見える

---

## 補完手法との組み合わせ

| 評価手法 | 適用分野 |
|----------|----------|
| **RAGAS** | RAG型AIの構造的信頼性評価 |
| **LLM-based評価** | 感情・トーン・創造性評価 |
| **人手評価** | 主観評価の標準化 |
| **BLEU/ROUGE** | 要約・翻訳の類似度測定 |

---

## まとめ

### ✅ RAGASの強み
- Ground Truth不要でも実用的評価が可能
- 分析・要約タスクに適している
- 客観的な信頼性評価を自動化


### ⚠️ 注意点
- RAGシステム限定
- 創造的・主観的タスクには不向き
- Retrieverの品質も重要

---

### 🚀 実用性
- 一つのスクリプトで評価可能
- AWS Lambda（コンテナ）でも対応可能
- 正解のないタスクでも効果的

---

# Thank you!

質問・相談はお気軽にどうぞ 