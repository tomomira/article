# 【解説】Webサイトからのデータ取得：静的 vs 動的コンテンツの違い

Webサイトから情報を自動で収集する「Webスクレイピング」を行う際、対象サイトの作りによってデータ取得の方法が大きく異なります。ここでは、その鍵となる「静的コンテンツ」と「動的コンテンツ」の違いと、それぞれのデータ取得方法について解説します。

## 1. 静的コンテンツ (Static Content)

Webページを開いた瞬間に、すべての情報がHTMLファイル内に記述されているコンテンツです。

-   **特徴**:
    -   ページのHTMLソースコード内にデータが直接埋め込まれている。
    -   誰がいつアクセスしても、表示される内容は同じ。
-   **具体例**:
    -   ニュース記事の本文
    -   企業の会社概要ページ
    -   URLが `.../report.pdf` のようになっているPDFファイルへの直接リンク
-   **データ取得方法**:
    -   Pythonの `requests` ライブラリのような、単純なHTTPクライアントでページのHTMLを取得し、`BeautifulSoup` などで解析するだけで簡単にデータを抽出できます。
    -   処理がシンプルで高速です。

```python
# 静的コンテンツの取得イメージ
import requests
from bs4 import BeautifulSoup

url = '静的なウェブページのURL'
response = requests.get(url)
soup = BeautifulSoup(response.text, 'html.parser')

# soupオブジェクトから必要なデータを抽出
title = soup.find('h1').text
print(title)
```

## 2. 動的コンテンツ (Dynamic Content)

Webページを読み込んだ後、JavaScriptというプログラムが動作することによって、後から追加・生成されるコンテンツです。

-   **特徴**:
    -   初期のHTMLにはデータが含まれておらず、JavaScriptによってデータが読み込まれ、ページが書き換えられる。
    -   ユーザーの操作（スクロール、クリックなど）に応じて、新しい情報が次々と表示される。
-   **具体例**:
    -   「Download historical data」のような、クリックするとデータが生成されるボタン
    -   TwitterやFacebookのような、下にスクロールすると新しい投稿が読み込まれる無限スクロール機能
    -   株価や天気予報など、リアルタイムで情報が更新される部分
-   **データ取得方法**:
    -   `requests` ライブラリでは、JavaScriptが実行されないため、後から生成されるデータを取得できません。
    -   `Selenium` や `Playwright` のような「ブラウザ自動化ツール」を使用する必要があります。これらのツールは、プログラム上で実際にブラウザ（Chromeなど）を起動し、ボタンのクリックやスクロールをシミュレートして、JavaScriptが完全に動作した後の最終的なWebページの状態からデータを取得します。
    -   処理が複雑になり、静的な場合に比べて時間もかかります。

```python
# 動的コンテンツの取得イメージ (Playwrightの場合)
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    
    page.goto('動的なウェブページのURL')
    
    # JavaScriptがデータを読み込むのを待つ
    page.wait_for_selector('データが表示される要素') 
    
    # ボタンをクリックするなどの操作
    page.click('#downloadButton')
    
    # 最終的なページのHTMLからデータを抽出
    html = page.content()
    print(html)
    
    browser.close()
```

## 3. 見分け方

-   **ブラウザのJavaScriptを無効にしてみる**: ChromeのデベロッパーツールなどでJavaScriptを無効にしてページを再読み込みした際に、見たい情報が表示されなくなれば、それは動的コンテンツです。
-   **「ページのソースを表示」で確認する**: ブラウザで右クリックして「ページのソースを表示」を選び、表示されたHTMLソースコード内にデータが見当たらない場合、動的コンテンツである可能性が高いです。

## まとめ

ご質問いただいた内容は、この「静的」と「動的」の違いによる制約が原因です。

| 特徴           | 静的コンテンツ                                 | 動的コンテンツ                                             |
| :------------- | :--------------------------------------------- | :--------------------------------------------------------- |
| **内容**       | HTML内に直接記述済み                           | JavaScriptにより後から生成・表示                         |
| **取得ツール** | `requests` 等のシンプルなライブラリでOK        | `Selenium` や `Playwright` 等のブラウザ自動化ツールが必要 |
| **取得の容易さ** | 簡単・高速                                     | 複雑・低速                                                 |
| **あなたの例** | ② PDFへの直接リンク (`...LNGPA.pdf`) | ① 「Download historical data」ボタン                     |

結論として、**「動的なボタンからのデータ取得には、JavaScriptを実行できるSeleniumやPlaywrightが必要」** というご認識は、完全に正しいです。