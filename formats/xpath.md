# XPath Cheat Sheet

## Overview

XPath（XML Path Language）は、XML/HTML ドキュメントの中から特定のノード（要素・属性・テキストなど）を選択するためのパス指向のクエリ言語です。実務では Web スクレイピング、Selenium/Playwright での要素特定、XSLT 変換などで使われます。

## When to Use

- HTML/XML から特定の要素・属性・テキストを抜き出したいとき
- Selenium/Playwright などで、CSS セレクタだけでは表現しにくい要素（テキスト内容や親方向）を特定したいとき
- XSLT でドキュメントを変換したいとき

## Basic Syntax

```text
document.evaluate("XPATH", document, null, XPathResult.ANY_TYPE, null)  # ブラウザ JS
$x("XPATH")                                                              # ブラウザ DevTools コンソール
tree.xpath("XPATH")                                                      # Python lxml
driver.find_element(By.XPATH, "XPATH")                                   # Selenium
```

## Common Examples

```xpath
/html/body/div            # ルートからの絶対パス
//div                      # ドキュメント中のどの階層でも良い div（すべて）
//div[@class="foo"]        # class属性が "foo" の div
//a/@href                  # すべての a タグの href 属性値
//book[price>35]/title     # price が 35 より大きい book の子要素 title
//*[@id="main"]            # タグ名を問わず id="main" の要素
```

## Frequently Used Options

```xpath
# --- 軸（axis）: 現在位置からどの方向のノードを見るか ---
child::div                 # 子要素（省略形: div）
descendant::div             # 子孫すべて（省略形: .//div）
parent::node()              # 親（省略形: ..）
ancestor::div                # 祖先
following-sibling::div       # 自分より後の兄弟
preceding-sibling::div       # 自分より前の兄弟
attribute::href               # 属性（省略形: @href）
self::node()                  # 自分自身（省略形: .）

# --- 述語（predicate）: [ ] の中で絞り込む。インデックスは1始まり ---
//li[1]                       # 最初の <li>（0番目ではなく1番目）
//li[last()]                  # 最後の <li>
//li[position() < 3]          # 最初の2つ
//a[contains(@href, "example.com")]   # href に "example.com" を含む a
//div[not(@class)]            # class属性を持たない div
//p[text()="hello"]           # テキストが完全一致する p
//p[contains(text(), "hello")]  # テキストに "hello" を含む p

# --- よく使う関数 ---
count(//li)                   # マッチしたノード数
string(//title)                # ノードの値を文字列として取得
normalize-space(//p)           # 前後の空白除去・連続空白を1つにまとめる
concat(//first, " ", //last)   # 文字列連結
```

## Notes

- 述語 `[ ]` の中のインデックスは **1始まり** です（プログラミング言語の 0 始まりの配列とは異なるので注意）。
- `//div` は正式には `/descendant-or-self::node()/div` の省略形で、「ルートから見てどの深さにあっても良い div」を意味します。要素の直下の子だけに絞りたい場合は `/` を使います。
- ブラウザの DevTools コンソールで `$x("//div[@class='foo']")` と打つと、その場で XPath のマッチ結果を配列で確認できます。
- [CSS セレクタ](css-selector.md)では書けない「テキスト内容での絞り込み」（`contains(text(), "...")`）や「親方向への移動」（`..`、`parent::`、`ancestor::`）が XPath では可能です。反対に、単純な階層指定だけであれば CSS セレクタの方が短く書けることが多いです。
- 多くのライブラリ（lxml/libxml2、ブラウザの `document.evaluate`）は **XPath 1.0** までの対応です。正規表現マッチ関数や日付関数などを含む XPath 2.0/3.1 を使うには Saxon などの別実装が必要になる場合があります。

## Related Links

- MDN (XPath): https://developer.mozilla.org/ja/docs/Web/XPath
- W3C XPath 1.0 Spec: https://www.w3.org/TR/xpath-10/
- W3Schools XPath Syntax: https://www.w3schools.com/xml/xpath_syntax.asp
