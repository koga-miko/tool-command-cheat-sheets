# CSS Selector Cheat Sheet

## Overview

CSS セレクタは、HTML/XML 文書の中から要素を選ぶためのパターン記法です。もともとは CSS でスタイルを当てる対象を指定するための構文ですが、`document.querySelector` や各種スクレイピングライブラリ、Selenium/Playwright での要素特定など、スタイリング以外の場面でも広く使われます。

## When to Use

- CSS でスタイルを当てる対象を指定したいとき
- ブラウザの JS（`querySelector`/`querySelectorAll`）や Selenium/Playwright で要素を取得したいとき
- Web スクレイピングで、階層構造から特定の要素を抜き出したいとき

## Basic Syntax

```text
document.querySelector("SELECTOR")               # ブラウザ JS（最初の1件）
document.querySelectorAll("SELECTOR")             # ブラウザ JS（すべて）
$("SELECTOR")                                      # jQuery / Chrome DevTools コンソール
soup.select("SELECTOR")                            # Python BeautifulSoup
driver.find_element(By.CSS_SELECTOR, "SELECTOR")   # Selenium
```

## Common Examples

```css
*              /* すべての要素 */
div            /* タグ名 */
.classname     /* class 属性で指定 */
#id            /* id 属性で指定 */
div.classname  /* タグ名 + クラス（AND 条件） */
div, p         /* 複数セレクタをまとめて指定（OR 条件） */

div p          /* 子孫結合子: div の中のどこかにある p */
div > p        /* 子結合子: div の直接の子である p */
div + p        /* 隣接兄弟結合子: div の直後にある p */
div ~ p        /* 一般兄弟結合子: div より後にある同階層の p */
```

## Frequently Used Options

```css
/* --- 属性セレクタ --- */
[href]                   /* href 属性を持つ要素 */
[href="https://x.com"]   /* 属性値が完全一致 */
[href^="https"]          /* 前方一致 */
[href$=".pdf"]           /* 後方一致 */
[href*="example"]        /* 部分一致 */
[lang~="en"]             /* スペース区切りの単語のうち "en" を含む */
[lang|="en"]             /* "en" または "en-" で始まる（言語コード用） */

/* --- 構造擬似クラス --- */
:first-child              /* 兄弟の中で最初の要素 */
:last-child                /* 兄弟の中で最後の要素 */
:nth-child(n)               /* 兄弟の中で n 番目（1始まり） */
:nth-child(2n+1)             /* 奇数番目（1, 3, 5, ...） */
:first-of-type / :nth-of-type(n)  /* 同じタグ名の兄弟の中で数える */
:not(.foo)                   /* .foo を持たない要素 */
:empty                        /* 子要素もテキストも持たない要素 */

/* --- 状態擬似クラス --- */
:hover / :focus / :checked / :disabled

/* --- 疑似要素: 要素そのものではなく、要素に付随する部分を指す --- */
::before / ::after           /* 要素の前後に生成される内容 */
::first-line / ::first-letter
```

## Notes

- CSS セレクタは基本的に「親から子・兄弟」への一方向にしか絞り込めません。子から親への移動（例: 「この要素を含む親」）や、要素のテキスト内容そのものによる絞り込みは標準の CSS セレクタではできません。これらが必要な場合は [XPath](xpath.md) を使います。
- `:nth-child(n)` は「その要素の兄弟全体の中での位置」を数えるのに対し、`:nth-of-type(n)` は「同じタグ名の兄弟の中での位置」を数えます。異なるタグが混在する兄弟では結果が変わるので注意してください。
- `:contains("text")` はテキスト内容で絞り込む便利な疑似クラスですが、**標準の CSS セレクタには存在しません**（jQuery/Sizzle など一部ライブラリの独自拡張です）。標準準拠のセレクタエンジンでは動かないことがあります。
- ブラウザの DevTools コンソールでは、Chrome なら `$$("div.foo")`、あるいは `document.querySelectorAll("div.foo")` でその場で動作確認できます。
- Scrapy や BeautifulSoup（soupsieve）など多くのスクレイピングライブラリは CSS セレクタと XPath の両方をサポートしています。単純な階層指定は CSS セレクタ、テキスト内容や親方向への絞り込みが必要な複雑な条件は XPath、と使い分けることが多いです。

## Related Links

- MDN (CSS セレクタ): https://developer.mozilla.org/ja/docs/Web/CSS/CSS_selectors
- W3C Selectors Level 4: https://www.w3.org/TR/selectors-4/
- Can I use（ブラウザ対応状況の確認）: https://caniuse.com/
