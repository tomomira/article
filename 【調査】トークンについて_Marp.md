---
marp: true
theme: default
paginate: true
class:
  - title
  - content
header: ''
footer: ''
---

<style>
/* グローバルスタイル */
section {
  font-family: 'Courier New', 'Monaco', 'Consolas', monospace;
  background: #2d3748;
  background-image: 
    radial-gradient(circle at 25% 25%, rgba(255,255,255,0.02) 2px, transparent 2px),
    radial-gradient(circle at 75% 75%, rgba(255,255,255,0.02) 2px, transparent 2px);
  background-size: 40px 40px;
  color: #f7fafc;
  padding: 60px;
  border: 8px solid #8b4513;
  border-radius: 12px;
  box-shadow: 
    inset 0 0 20px rgba(0,0,0,0.3),
    0 0 30px rgba(0,0,0,0.5);
  position: relative;
}

/* 黒板の木枠効果 */
section::before {
  content: '';
  position: absolute;
  top: -8px;
  left: -8px;
  right: -8px;
  bottom: -8px;
  background: linear-gradient(45deg, #8b4513 0%, #a0522d 25%, #8b4513 50%, #654321 75%, #8b4513 100%);
  border-radius: 16px;
  z-index: -1;
}

/* タイトルスライド */
section.title {
  background: #1a202c;
  text-align: center;
  justify-content: center;
  align-items: center;
  color: #f7fafc;
}

section.title h1 {
  font-size: 2.8em;
  margin-bottom: 0.5em;
  text-shadow: 
    2px 2px 0 rgba(255,255,255,0.1),
    0 0 20px rgba(255,255,255,0.2);
  color: #ffffff;
  font-weight: bold;
  letter-spacing: 1px;
}

section.title h2 {
  font-size: 1.8em;
  opacity: 0.9;
  font-weight: normal;
  color: #e2e8f0;
  margin-bottom: 20px;
}

section.title h3 {
  font-size: 1.2em;
  color: #a0aec0;
  font-style: italic;
}

/* 通常スライド */
section.content {
  background: #2d3748;
  color: #f7fafc;
}

/* 見出しスタイル（チョーク風） */
h1 {
  color: #ffffff;
  font-size: 2.5em;
  margin-bottom: 40px;
  text-align: center;
  text-shadow: 
    2px 2px 0 rgba(255,255,255,0.1),
    0 0 15px rgba(255,255,255,0.3);
  border-bottom: 3px solid #ffffff;
  padding-bottom: 15px;
  font-weight: bold;
  letter-spacing: 1px;
}

h2 {
  color: #ffd700;
  font-size: 1.8em;
  margin-top: 40px;
  margin-bottom: 20px;
  text-shadow: 
    1px 1px 0 rgba(255,215,0,0.2),
    0 0 10px rgba(255,215,0,0.4);
  font-weight: bold;
  text-decoration: underline;
  text-decoration-color: #ffd700;
}

h3 {
  color: #87ceeb;
  font-size: 1.4em;
  margin-top: 30px;
  margin-bottom: 15px;
  text-shadow: 
    1px 1px 0 rgba(135,206,235,0.2),
    0 0 8px rgba(135,206,235,0.3);
}

/* リストスタイル（手書き風） */
ul {
  list-style: none;
  padding-left: 0;
  margin: 20px 0;
}

li {
  margin-bottom: 18px;
  padding-left: 35px;
  position: relative;
  font-size: 1.1em;
  line-height: 1.6;
  color: #e2e8f0;
  text-shadow: 1px 1px 0 rgba(255,255,255,0.1);
}

li:before {
  content: "•";
  color: #ffd700;
  position: absolute;
  left: 0;
  font-size: 1.5em;
  text-shadow: 
    1px 1px 0 rgba(255,215,0,0.2),
    0 0 10px rgba(255,215,0,0.4);
}

/* 数字付きリスト */
ol {
  list-style: none;
  counter-reset: item;
  padding-left: 0;
}

ol li {
  counter-increment: item;
  margin-bottom: 18px;
  padding-left: 40px;
  position: relative;
}

ol li:before {
  content: counter(item);
  color: #87ceeb;
  position: absolute;
  left: 0;
  top: 0;
  background: #87ceeb;
  color: #2d3748;
  width: 25px;
  height: 25px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  font-size: 0.9em;
  box-shadow: 0 0 10px rgba(135,206,235,0.3);
}

/* 強調スタイル（蛍光ペン風） */
strong {
  color: #ffd700;
  background: linear-gradient(transparent 50%, rgba(255,215,0,0.2) 50%);
  padding: 2px 4px;
  text-shadow: 
    1px 1px 0 rgba(255,215,0,0.2),
    0 0 10px rgba(255,215,0,0.4);
  font-weight: bold;
}

/* 重要な強調（赤チョーク風） */
.important {
  color: #ff6b6b;
  background: linear-gradient(transparent 50%, rgba(255,107,107,0.2) 50%);
  padding: 2px 4px;
  text-shadow: 
    1px 1px 0 rgba(255,107,107,0.2),
    0 0 10px rgba(255,107,107,0.4);
  font-weight: bold;
}

/* コードブロック（手書き風） */
code {
  background: rgba(0,0,0,0.3);
  color: #87ceeb;
  padding: 4px 8px;
  border-radius: 4px;
  font-family: 'Courier New', monospace;
  font-size: 0.9em;
  border: 1px dashed #87ceeb;
  text-shadow: 0 0 5px rgba(135,206,235,0.3);
}

pre {
  background: rgba(0,0,0,0.4);
  color: #87ceeb;
  padding: 20px;
  border-radius: 8px;
  overflow-x: auto;
  border: 2px dashed #87ceeb;
  margin: 20px 0;
  text-shadow: 0 0 5px rgba(135,206,235,0.3);
}

/* テーブル（手書き風） */
table {
  border-collapse: collapse;
  width: 100%;
  margin: 20px auto;
  border: 2px solid #ffffff;
  table-layout: fixed;
}


th, td {
  border: 1px solid #a0aec0;
  padding: 12px;
  text-align: center;
  color: #ffffff;
}

th {
  background: #2d3748;
  color: #ffffff;
  font-weight: bold;
  text-shadow: 
    1px 1px 0 rgba(0,0,0,0.3),
    0 0 8px rgba(255,255,255,0.3);
}

td {
  background: #2d3748;
}


/* 2カラムレイアウト */
.columns {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 40px;
  align-items: start;
}

/* メモ帳風カード */
.note {
  background: rgba(255,255,255,0.1);
  padding: 20px;
  border-radius: 8px;
  border: 2px dashed #ffffff;
  margin: 20px 0;
  position: relative;
  backdrop-filter: blur(5px);
}

.note:before {
  content: '📝';
  position: absolute;
  top: -10px;
  left: 10px;
  background: #2d3748;
  padding: 5px;
  border-radius: 50%;
}

/* 情報ボックス（青チョーク風） */
.info {
  background: rgba(135,206,235,0.1);
  border: 2px dashed #87ceeb;
  border-radius: 8px;
  padding: 15px;
  margin: 20px 0;
  color: #87ceeb;
  text-shadow: 0 0 10px rgba(135,206,235,0.3);
}

.info:before {
  content: '💡';
  font-size: 1.5em;
  margin-right: 10px;
}

/* フッター */
footer {
  position: absolute;
  bottom: 20px;
  right: 20px;
  font-size: 0.9em;
  color: #a0aec0;
  text-shadow: 1px 1px 0 rgba(255,255,255,0.1);
}
</style>

<!-- _class: title -->

# 【調査】トークンについて

## AIにおけるトークンの基本概念から実用的な活用方法まで

### 2024年末時点の情報

---

<!-- _class: content -->

# 📚 今日の授業内容

1. **トークンの基本概念** - 文字ではなく意味のある塊として分割される単位
2. **単位の換算** - 1M = 1000k = 100万トークンの関係
3. **文字数との関係** - 英語4文字/日本語2-3文字程度の目安
4. **具体的なイメージ** - 小説5-10冊分の文章量
5. **料金への影響** - 入力・出力トークンの料金計算
6. **実用的なコツ** - トークン節約術と注意点
7. **モデル別特徴** - コンテキスト長と用途別選び方

---

<!-- _class: content -->

# トークンとは？
<style scoped>
section {
  font-size: 2.0em;
}
</style>
## **トークン**は、AIが文章を処理する際の基本単位です

文字そのものではなく、**単語や文字の組み合わせを意味のある塊として分割した単位**のことを指します。

### 📝 例：トークンの分割例

<div class="note">

- **英語**：「Hello world」→ 「Hello」「world」（2トークン）
- **日本語**：「こんにちは」→ 「こん」「にち」「は」（3トークン）

</div>

---

<!-- _class: content -->

# 📊 単位の換算
<style scoped>
section {
  font-size: 1.8em;
}
</style>
## 基本的な単位関係

```
1M tokens = 1,000,000 tokens = 1,000k tokens
- M（メガ）= 100万
- k（キロ）= 1,000
```

<div class="info">

**料金表示での使われ方**
- **1M tokens** = **1,000k tokens** = **100万トークン**
- どちらも同じ意味で、表記が異なるだけ

</div>

---

<!-- _class: content -->

# 🔤 トークンと文字数の関係

## 言語別の目安

| &nbsp; &nbsp;  言語 &nbsp; &nbsp;  | &nbsp; &nbsp;  1トークンあたりの文字数 &nbsp; &nbsp;   | &nbsp; 100万トークンの文字数 &nbsp;  |
|------|----------------------|--------------------- |
|**英語** | 約4文字 | 約400万文字 |
|**日本語** | 約2-3文字 | 約200-300万文字 |

---

<!-- _class: content -->

# ⚠️ 影響要因と注意点
<style scoped>
section {
  font-size: 1.8em;
}
</style>
## トークン消費に影響する要因

- **専門用語**：より多くのトークンを消費
- **記号・数字**：トークン消費が増加
- **文章の種類**：技術文書は一般文書より消費が多い

## 💡 効率的な使用のポイント

- 簡潔で分かりやすい表現を心がける
- 不要な装飾語を避ける

---

<!-- _class: content -->

# 📖 具体的なイメージ
<style scoped>
section {
  font-size: 2.0em;
}
</style>
## 日本語での量感

<div class="columns">
<div>

- **小説1冊**：約10-20万文字
- **100万トークン** ≈ **小説5-10冊分**
- **1,000トークン** ≈ **A4用紙2-3枚程度**

</div>
<div>

### 実用例
```
例：ChatGPTとの会話
- 質問：50トークン
- 回答：200トークン
- 合計：250トークン（1回のやり取り）
```

</div>
</div>

---

<!-- _class: content -->

# 💰 AIサービスの料金への影響

## 料金計算の仕組み

- **入力トークン**：AIに送信するテキスト
- **出力トークン**：AIが生成するテキスト
- 多くのサービスで**出力料金 > 入力料金**

---

<!-- _class: content -->

# 💵 料金例（GPT-4.1の場合）

```
入力料金：$2.00/1,000k tokens
出力料金：$8.00/1,000k tokens

10万トークンの場合：
- 入力：$0.20
- 出力：$0.80
```

<div class="important">

**出力の方が4倍高い**ことに注意！

</div>

---

<!-- _class: content -->

# 🎯 実用的な使い方のコツ
<style scoped>
section {
  font-size: 1.7em;
}
</style>
## トークン節約術

1. **簡潔な質問**：不要な情報を削る
2. **分割質問**：長い文章を複数回に分ける
3. **要点整理**：核心部分のみを伝える

## ⚠️ トークン消費の注意点

- **長いプロンプト**：大量のトークンを消費
- **会話履歴**：過去のやり取りも含まれる
- **添付ファイル**：画像・文書も大量消費

---

<!-- _class: content -->

# 🤖 モデル別の特徴

## コンテキスト長の違い

- **GPT-4.1**：100万トークン（長文処理可能）
- **Claude 4**：20万トークン（中程度）
- **Gemini 1.5 Pro**：200万トークン（超長文対応）

---

<!-- _class: content -->

# 🎯 用途別の選び方

<div class="columns">
<div>

### 短文処理向け
- 軽量モデル（Haiku、Flash）
- コスト効率重視

### 長文分析向け
- 大容量モデル（Pro、Sonnet）
- 高精度重視

</div>
<div>

### コスト重視
- 効率モデル（mini、Flash）
- 頻繁な利用に最適

</div>
</div>

---

<!-- _class: content -->

# 📝 まとめ

- **1M tokens = 100万トークン ≈ 日本語で200-300万文字**
- **実用イメージ：小説5-10冊分の文章量**
- **料金に直結**するため、効率的な使用が重要
- **用途に応じたモデル選択**でコスト最適化が可能

---

<!-- _class: title -->

# ありがとうございました

## **質問・疑問点があればお気軽にどうぞ**

### *2024年末時点の情報に基づく。各サービスの料金やトークン計算方法は変更される可能性があります。* 