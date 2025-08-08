---
title: "RAGAS 評価導入方法"
source: "https://chatgpt.com/c/6858a775-087c-800c-9ad3-a7efd9e34a10"
author:
published:
created: 2025-06-23
description: "ChatGPT helps you get answers, find inspiration and be more productive. It is free to use and easy to try. Just ask and ChatGPT can help with writing, learning, brainstorming and more."
tags:
  - "clippings"
---
生成AIやRAGを評価するツールで、RAGASというのがありますが、これはどういう風に導入しますか？ 最終的にやりたいことは、 ①生成AIに質問する（分析でも要約でも） ➁上記①の生成AIをRAGASで評価して、評価内容をアウトプット ③上記➁の評価結果をテキスト形式でもいいのでアウトプット

---

RAGAS（Retrieval-Augmented Generation Assessment Suite）は、RAGシステム（Retriever + Generator）を評価するためのPythonベースのツールです。質問応答、要約、情報検索などのRAGパイプラインの性能を定量的に評価できます。

以下、あなたの要件に沿った導入・使用手順を詳しく説明します。

---

## ✅ 前提条件

- Python 3.8 以上
- 基本的なPython環境（pip、仮想環境など）

---

## 🔧 RAGAS の導入方法

### 1\. 仮想環境を作成（任意）

### 2\. RAGAS のインストール

また、評価に必要なモデルがあるため、必要に応じて以下もインストール：

---

## 📦 RAGAS を使って評価する基本的な流れ

RAGAS は以下のような構造のデータセットを必要とします：

---

## ✅ あなたの要件に対応する処理フロー

### ① 生成AIに質問する

たとえば、RAGシステムに質問して答えと参照文書（context）を取得する処理を書く：

---

### ➁ RAGASで評価する

---

### ③ 評価結果をテキスト形式で出力

またはファイル出力：

---

## 💡 補足：複数QA評価への対応

複数のQAペアで評価したい場合は、 `Dataset.from_dict()` にリストを渡せばOKです。

---

## ✨ 出力例

---

## 🚀 まとめ

| ステップ | 内容 |
| --- | --- |
| ① | RAGで質問・回答・contextを取得 |
| ② | RAGASで評価実行（Dataset形式に整形） |
| ③ | 結果を標準出力またはテキストで保存 |

---

必要であれば、上記をスクリプト形式でまとめて提供できます。どうしますか？

---

三つ質問させてください。 ひとつめ：①から③の対応したいことは連携して一つのスクリプトでできますか ふたつめ：ground\_truthの正しい回答が分からないときはどうせすればいいですか？ みっつめ：pip install ragasとありますが、容量は大きいですか？

---

ありがとうございます。順番に詳しくお答えします。

---

### ✅ 一つめ：①〜③は連携して一つのスクリプトにできますか？

**→ はい、完全に可能です。**

以下のような処理を一つのPythonスクリプトでまとめることができます：

1. 質問を投げる（RAGパイプライン or ダミーで用意）
2. 回答・コンテキストを取得
3. RAGASで評価
4. 結果を標準出力 or ファイル出力

**サンプルスクリプトの構成例：**

---

### ✅ ふたつめ：ground\_truth が分からないときは？

RAGASでは `faithfulness` や `context_precision` は **ground\_truth がなくても評価できます** 。

#### 方法：

- `ground_truth` を除外して `answer_relevancy`, `faithfulness`, `context_precision` など一部のメトリクスだけ使う。

#### ヒント：

- `faithfulness` ：回答が context に忠実か
- `context_precision` ：正しい context をどれだけ使っているか
- `answer_relevancy` ：質問に対して答えがどれだけ関連しているか  
	→ **ground\_truth が不要な評価も多いので安心して使えます。**

---

### ✅ みっつめ：pip install ragas の容量は大きいですか？

**→ やや大きめですが、通常のPC開発環境なら問題ありません。**

#### 内訳：

- `ragas` 自体は軽量（数MB）
- ただし依存パッケージに以下が含まれるためやや重いです：

| ライブラリ | 用途 | サイズ目安 |
| --- | --- | --- |
| `transformers` | モデル利用 | 数百MB（モデルダウンロード時） |
| `sentence-transformers` | 埋め込み生成 | 数十〜百MB |
| `datasets` | データ管理 | 数十MB |
| `evaluate` | 評価指標処理 | 数十MB |
| （モデル） | 評価時に自動DL | 数百MB〜1GB（初回のみ） |

#### 合計目安：

- 初回：300MB〜1.5GB（評価用モデル込み）
- 2回目以降：キャッシュ再利用で軽くなります

---

## 🔚 まとめ

| 質問 | 回答 |
| --- | --- |
| ① 連携できる？ | ✅ 一つのスクリプトでOKです |
| ② ground\_truth 不明な場合？ | ✅ 一部メトリクスで不要。faithfulness 等で評価可能 |
| ③ インストール容量は？ | ⚠️ モデル含めると大きめ（~1GB）だが一般的なPCで問題なし |

---

ご希望があれば、 **複数QA評価のテンプレートスクリプト** や、 **RAG生成AIの簡易実装（FAISS + LLM）との連携例** もご提供できます。ご希望ですか？

---

みっつめ：pip install ragas の容量は大きいですか？の質問ですが、これをAWS Lambdaで対応するとしたら、大きすぎて難しいですか？ Lambdaをコンテナ化するという案もあると聞いたことがありますが。

---

とても良い質問です。「RAGASをAWS Lambdaで動かせるか？」という観点について、結論と理由を順を追って解説します。

---

## ✅ 結論：そのままでは厳しいが、コンテナ化すれば可能です

RAGASとその依存関係（特に `transformers` や `sentence-transformers` ）はサイズが大きいため、 **通常のLambda（zip形式）では制限を超える可能性が高いです** 。

---

## 🔧 Lambdaの制約（2025年時点）

| 制限項目 | 上限 |
| --- | --- |
| Lambda関数のデプロイパッケージ（zip） | **250MB（解凍後）** |
| Lambdaコンテナイメージ | **最大10GB** |
| 実行時間 | 最大15分 |
| メモリ | 最大10GB（vCPUも比例拡張） |

→ **RAGASは依存が多く250MBを軽く超えるので、zipデプロイは不適。コンテナ推奨。**

---

## ✅ 対応策1：Lambda + コンテナイメージ

Lambdaをコンテナとして使うと、最大10GBの環境でRAGAS + モデルを動かせます。

### 手順概要：

1. **Dockerfile作成**  
	ベースイメージは `public.ecr.aws/lambda/python:3.11` などを使う
2. **RAGASと依存をインストール**
	`requirements.txt` ：
3. **ECRにプッシュ → Lambdaにデプロイ**

---

## ✅ 対応策2：バッチ処理 or SageMakerにオフロード

- RAGAS評価はバッチ処理（長時間かかる）なので、Lambdaにこだわらず、 **以下のような代替も有効** です：

| 方法 | 特徴 |
| --- | --- |
| **SageMaker Notebook / Processing Job** | モデル評価・データ処理に最適。モデルサイズの制限なし |
| **ECS（Fargate）やBatch** | スポット実行。Lambdaの代替として柔軟 |
| **LambdaからSQSやStep Functionsで連携** | 「リクエスト受け → RAGAS評価バッチへ渡す」構成が現実的 |

---

## 🔍 まとめ

| 方法 | RAGAS評価に適しているか | 備考 |
| --- | --- | --- |
| Lambda（zip） | ❌ 厳しい（容量超過） |  |
| Lambda（コンテナ） | ✅ 対応可能（〜10GB） |  |
| SageMaker / ECS | ✅◎ 非同期・大規模に最適 |  |
| Lambda → SQS → バッチ | ✅◎ 現実的な実装構成 |  |

---

## 🚀 ご提案：もし使いたいなら…

「Lambdaコンテナ化＋軽量モデル（MiniLMなど）＋非同期評価」で構成するのが、 **実運用とコストのバランスが取れた構成** です。

---

必要であれば、 **Lambdaコンテナ化用のDockerfileテンプレート** や、\*\*評価APIとして公開するサンプル構成（Lambda + API Gateway）\*\*もお渡しできます。希望されますか？

---

ふたつめ：ground\_truth が分からないときは？で、これが不要なケースとありますが、自社で考えている評価対象はほぼ正解がありません。というのも、分析して～とか要約して～というのが多いので、そもそも正解（アウトプットの正しさをあらかじめ用意できない）がわからないというのがその意味となります。そのため、できるだけ、評価するRAGASが、分析や要約をした生成AIが信頼できるAIであるかの振る舞いを検知したいので、groud\_truthは分からないとなりますが、大丈夫ですか？

---

はい、その前提 \*\*（分析・要約などで正解が事前に存在しない）\*\*は、RAGASの本来の強みと非常によくマッチしています。結論から言えば：

---

### ✅ ground\_truth がなくても、RAGASで十分実用的な評価が可能です

---

## 🎯 なぜ ground\_truth がなくても評価できるのか？

RAGASは以下のような\*\*「ground\_truth 非依存の評価指標」 **を備えており、** 「アウトプットが正確そうか・信頼できるか」\*\*を推定できます。

| メトリクス名 | 依存する要素 | 説明 |
| --- | --- | --- |
| **faithfulness** | context・answer | 回答が提示されたコンテキストに忠実であるか？（幻覚検知） |
| **answer\_relevancy** | question・answer | 回答が質問と関連しているか？（適合度） |
| **context\_precision** | context・answer | 回答にとって重要なcontextが含まれているか？（冗長性なし） |
| **context\_recall** | answer・context・ **（optional）ground\_truth** | 回答に必要な情報がcontextに含まれているか？   ※ground\_truthがあればより正確に測定可能だが、省略可 |

---

## 🔍 たとえば、以下のようなタスクでも評価できます：

| タスク例 | ground\_truth なしで評価できるか？ | コメント |
| --- | --- | --- |
| 「この文書を要約して」 | ✅ 可能 | faithfulness で幻覚の有無をチェック |
| 「このログから原因を分析して」 | ✅ 可能 | answer\_relevancy + context\_precision が重要 |
| 「このニュースのポイントを抽出して」 | ✅ 可能 | 冗長性なく正確な要約かを確認できる |

---

## ✳️ ポイント

### 🎯 RAGASは「出力が正解っぽく見えるか」ではなく、「\*\*根拠に忠実であるか（faithfulness）」「質問に合っているか（answer relevancy）」を測定するツール」なので、

✅ **ground\_truth を用意できないナレッジ系AIの評価に適している**  
✅ **評価の自動化・客観化ができる**

---

## 📌 注意点（補足）

- **完全にground\_truthなしで万能に信頼性を担保できるわけではありません** 。あくまで「RAGによって\*\*与えられた文脈に対して、どれだけ真っ当な答えをしているか」を測っているだけです。
- よって、 **RAGのretrieverが誤った文脈を出した場合は、その上のfaithfulnessも良く見えてしまうことがある** ため、retrieverの質も合わせて監視する必要があります。

---

## ✅ 結論

> 「正解がないタスク（要約・分析・洞察）」においても、RAGASは **非常に有効な評価手段** となり得ます。

特に使えるメトリクスは：

- `faithfulness` （生成が根拠に忠実か？）
- `answer_relevancy` （質問に合っているか？）
- `context_precision` （余計な情報が多くないか？）

これらを組み合わせて、 **「信頼できる回答をする生成AIであるか」を数値的に可視化** できます。

---

必要であれば、「ground\_truth なしでも動作する評価テンプレート」もすぐに作成します。ご希望ですか？

---

テンプレートを作成してほしいです。 また、逆にRAGASが評価できないパターンはどのようなものがありますか？

---

了解しました！以下に **「ground\_truth なしで使える RAGAS 評価テンプレート（Pythonスクリプト）」** を提供します。そのあとで、 **RAGASが評価に向かない／難しいパターン** もご説明します。

---

## ✅ 【テンプレート】Ground TruthなしでのRAGAS評価スクリプト

このテンプレートでは、分析・要約系タスクなどにおいて「正解のない回答」がcontextに忠実か、質問に関連しているかを定量的に評価します。

```python
python# ragas_eval_template.py

from datasets import Dataset
from ragas import evaluate
from ragas.metrics import (
    faithfulness,
    answer_relevancy,
    context_precision,
    context_recall
)

# ---- 1. 評価対象のデータを準備 ----
# あなたの生成AIの出力と、その出力に使ったcontextを入力してください
question = "このレポートの売上推移から、重要な傾向を分析してください。"

# 生成AIが参照した文脈（検索やデータ抽出で取得したもの）
contexts = [
    "2023年Q1の売上は前年同期比で12%増加し、特に北米市場が牽引。",
    "Q2ではアジア市場での新製品投入により前年比18%の成長が見られた。",
    "一方で、Q3は為替の影響もあり伸び率が鈍化し、前年比で2%増に留まった。"
]

# 実際に生成AIが出力した回答
answer = "全体として前年比で売上は増加傾向にあり、特にQ2のアジア市場が大きく貢献していますが、Q3では為替の影響で成長が鈍化しています。"

# ---- 2. データセット作成（ground_truthは使わない） ----
data = Dataset.from_dict({
    "question": [question],
    "contexts": [contexts],
    "answer": [answer]
    # ground_truth は intentionally omitted
})

# ---- 3. RAGAS 評価（ground_truth不要なメトリクスのみ） ----
results = evaluate(
    data,
    metrics=[
        faithfulness,
        answer_relevancy,
        context_precision
        # context_recallは一応ground_truthがあると精度が上がる
    ]
)

# ---- 4. 結果出力 ----
for metric, score in results.items():
    print(f"{metric.name}: {score:.4f}")

with open("ragas_eval_result.txt", "w") as f:
    for metric, score in results.items():
        f.write(f"{metric.name}: {score:.4f}\n")
```

---

## 📌 使い方補足

- **このテンプレートは完全にground\_truthなしで動作** します。
- 1件ずつの評価ですが、複数件を評価する場合はリスト化すればOK（複数QAに対応可）。

---

## ❌ 【RAGASが評価に向かないパターン】

以下のようなケースでは、RAGASだけでは評価が難しい or 不正確になることがあります：

---

### 1\. 🔍 Contextなしの通常のLLM回答

例：「ChatGPTに自由に質問して答えを返してもらう」  
→ RAGASは **RAG（Retriever + Generator）に限定した評価フレームワーク** なので、 **文脈（contexts）が存在しない** 場合は使えません。

---

### 2\. 🎨 創造的な出力（ストーリー生成・詩・広告文案など）

例：「この製品を魅力的に紹介するキャッチコピーを作って」  
→ contextやquestionとの忠実性や関連性では測りづらく、 **主観的な質の評価が必要** になるため、RAGASの指標は適しません。

---

### 3\. 📄 Contextの品質が悪い or 不正確

→ RAGASは「生成AIが文脈に忠実だったか」を評価するだけなので、 **retrieverが誤情報を返した場合は、それでもfaithfulnessは高く見える** ことがあります。

---

### 4\. 🧠 推論・価値判断・主観を伴う回答

例：「このニュース記事について賛成か反対か述べよ」  
→ 正解が存在しない主観的な応答には、評価がうまく機能しません。

---

## 🎯 補足：他の手法と併用を検討してもよい

| 評価手法 | 適している内容 |
| --- | --- |
| **RAGAS** | RAG型AIの構造的な信頼性（再現性・忠実性・関連性）評価 |
| **LLM-based 評価（GPTに採点させるなど）** | 感情・トーン・創造性などの人間的尺度 |
| **人手評価（スケール）** | 主観評価の標準化が必要な場合 |
| **BLEU / ROUGE / BERTScore** | 要約や翻訳など、出力と正解の類似度が測れる場合 |

---

## ✅ まとめ

| 項目 | 結論 |
| --- | --- |
| ground\_truth なし評価 | ✅ RAGASは得意。テンプレートあり |
| 向かないパターン | ❌ 非構造的出力・主観的創作・contextなし |
| 補完手法 | 🔁 LLM採点 or 人手評価との組み合わせで補強可 |

---

必要であれば、 **複数QA対応版テンプレート** や、 **RAG構築部分（検索＋生成）の組み込み例** も追加可能です。ご希望ですか？