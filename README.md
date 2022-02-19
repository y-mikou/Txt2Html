# Txt2Html
一定程度規則に則って書かれたプレーンテキストに対し、CSS組版とWEB表示のソースを作り分ける素材となるHTMLタグを付与する。あるいはそれを除去してプレーンテキストに戻す。

専用のCSSを用意し、htmlに対して付与する情報ではそれを使用する。専用CSSは組版用/WEB表示用それぞれで競合しないようクラス分けを行う。

## スコープアウト
組版用htmlのタグを挿入するに当たり、縦書きと横書きを意識すること

## ターゲット
- プレーンテキストで書かれた小説
- WEBで公開するために、htmlにしたい
- 同人誌として組版する際に、CSS組版を使用したい

> 「プレーンテキストで書かれた」には、上記の通り、一定程度の規則を要求します

## 目的
WEBで公開するhtmlを作成したりCSS組版を実施する際、
- 元の生テキスト
- CSS組版されたデータ
- WEB用にスタイリングされたデータ

を行き来する、あるいは元データを一元的に扱うために、形式化された変換(可能であれば可逆変換)が求められるため。

## 形式
対象のテキストファイル一つと、変換方向の2つを引数として指定する、bashスクリプト
- 対象ファイル
- 変換方向
  - htmlタグを付与する
  - htmlタグを除去する

---

# .txtと.htmlの約物定義
可逆変換を実現するため、なるべく多義性を持たないよう対定義する。

## ■改行について
- txtの改行…ハード改行(`\n`)
- htmlの改行…`br`タグ+ハード改行(`\n`)

> `\r`、`\r\n`は予め`\n`へ置換する

## ■空行について
- txtの空行1行(行頭`\n`) = htmlの行頭`br`タグ(`1行空けクラス`指定)
- txtの空行2行(2連続する行頭`\n`) = htmlの行頭`br`タグ(`2行空けクラス`指定)
> txtの空行3行以上…章区切りへ変換する
> 
> 行頭クラス指定なしbrタグは、`1行空けクラス`を**強制適用**する

## ■段落について
- txtの段落
  - 行頭全角空白文字から、次の行頭全角空白文字の手前まで 
  - 行頭全角空白文字から、章区切りの手前まで

- htmlの段落
  - `p`タグで囲まれた範囲

## ■章について
章の大小、段階は設けない。

htmlにおいては`見出し`と混同されがちだが、明確に区別する。

`h`系タグは、章のタイトルの記載には使用しても、**章の区切りにはしない**。

- txtの章
  - 3行以上の空行から、次の3行以上の空行の手前まで
  - `§`などの指定の文字で始まる1行から、次の`§`などの指定の文字で始まる行の手前まで
> 3行以上連続する空行は、予め`§`の文字へ置換する

- htmlの章
  - `章クラス`を指定した`div`タグに囲まれた範囲
    - `section`タグは通常のWEB表示の際、サイト側で使用することが多いため使用を避ける。

## ■章タイトルについて
- txtでの章タイトル
  - `$`などの章を指定する文字で始まる1行
    - 3行以上の空行による章は、予め`§`に置換されている想定
- htmlでの章タイトル
  - `章クラス`を指定した`div`タグの直後に現れる`h`系タグ一つに囲まれた範囲

## ■改ページについて
- txt
  - [newpage]の文字
- html
  - `終了後改ページクラス`を指定した章の終わり
  - `改ページクラス`を指定した**空の**`div`タグ

## ■ルビについて
- txt
  - `｜母字《ルビ》`による形式
- html
  - `<rb>母字<rt>ルビ</rt></rb>`による形式
> 母字とルビの距離については操作を諦める。
> 組版時には行間を縛る障害になるが、技術的にコストが高い

## ■縦中横について
- txt
  - `<tcy>yoko</tcy>`による形式
- html
  - `<span class="tcy">yoko</span>` による形式

---

# 処理概要

## 方針

基本的に上記の置換を行うのみ。

バックアップは強制的に作成する。

同名ファイルがあれば上書きする。

## 概要

``` mermaid 
flowchart TD

開始 --> 引数_ファイルの判定
引数_ファイルの判定 --ファイルあり--> 引数_変換方法の判定1 
引数_ファイルの判定 --ファイルなし--> エラー表示
引数_変換方法の判定1 --引数=1か2--> バックアップ作成
引数_変換方法の判定1 --引数=1でも2でもない--> エラー表示
バックアップ作成 --> 引数_変換方法の判定2
引数_変換方法の判定2 --引数=1--> TextToHtmlへ
引数_変換方法の判定2 --引数=2--> HtmlToTextへ

```

``` mermaid 
flowchart TD
TextToHtml --> 事前変換
事前変換 --> 本変換(記載中)

```

``` mermaid 
flowchart TD
HtmlToText --> htmlタグを除去(記載中)

```


---

……加筆中……

