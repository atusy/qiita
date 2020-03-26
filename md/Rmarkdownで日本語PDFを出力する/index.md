#はじめに

Rmarkdownで日本語を含むPDFを作成する際、躓きやすいポイントと対処方法を紹介する。

Rmakrdownを未だ導入していない人は
http://gihyo.jp/admin/serial/01/r-markdown/0002
あたりを参考にして欲しい。

RMarkdownを使えば、

-  Markdown記法を中心としつつ、数式にTeX記法、メタデータにYAML記法と、柔軟且つ容易でキレイなテキストの作成が可能
-  Rコードが自動着色される
-  Rコード実行結果の添付される
-  html, docx, pdfなど適宜出力形式を選択できる

といったメリットがある。

このうち、PDFの出力は、TeX環境の事前準備が必要な上、日本語の取り扱いが若干トリッキーなのだ。

#参考にしたWebサイト

-  [miyazakikenji: R Markdown に日本語pdf](https://miyazakikenji.wordpress.com/2015/08/14/r-markdown-%E3%81%AB%E6%97%A5%E6%9C%AC%E8%AA%9Epdf/)
-  [トライフィールズ: Ubuntu,R knitrで日本語のPDFを出力するための設定](http://www.trifields.jp/how-to-set-up-for-outputting-a-pdf-of-the-japanese-at-knitr-in-ubuntu-1404-and-r-1615)
-  [コタの科学日記: pandoc + xelatex で日本語 PDF を作る](http://cotaro-science.blogspot.jp/2017/03/pandoc-xelatex-pdf.html)

#Rmarkdown環境の用意

http://gihyo.jp/admin/serial/01/r-markdown/0002
を参照

#TeX環境の用意

メジャーなTeX環境には

-  TeX Live
    -  世界的に最もメジャー
    -  昨今では日本語組版も十全に行える
-  MikTex
    -  RStudioオススメ
    -  日本語対応はほどほどらしい
-  W32Tex
    -  日本で最もメジャー
    -  日本語組版も安心
    -  インストール後、PATHを通す必要がある(Windows)

などがある。
今回はTeX Live 2016を用いた。
インストーラは日本語でも動き、簡単にインストールできる。
ただし、3時間以上かかるので、時間があるときに行うことを勧める。

#YAMLでLaTeXエンジンを選ぶ

Rmarkdownで利用できるLaTeXエンジンには

-  pdflatex
    -  日本語不可
-  xelatex
    -  日本語可
-  lualatex
    -  日本語可

の三種類。
実用上はxelatexかlualatexとなる。
xelatexはlualatexよりも出力が3倍程度速いらしい(http://cotaro-science.blogspot.jp/2017/03/pandoc-xelatex-pdf.html)

##xelatexを用いる場合

.Rmdファイル**冒頭**に以下のYAMLを記載する。
YAMLには他にもタイトルや著者などのメタデータを記入できる。

```yaml
---
output:
  pdf_document: 
    latex_engine: xelatex 
header-includes: 
  - \usepackage{bookmark} 
  - \usepackage{xltxtra} 
  - \usepackage{zxjatype} 
  - \usepackage[ipa]{zxjafont} 
---
```

ネット上では`  - \usepackage{bookmark}`を読み込まない例も見受けられたが、当方ではこれがないと失敗した。

または、

```yaml
---
output:
  pdf_document: 
    latex_engine: xelatex 
documentclass: bxjsarticle
classoption: xelatex,ja=standard
geometry: no
---
```

でもよい。

##lualatexを用いる場合

.Rmdファイル**冒頭**に以下のYAMLを記載する。

```yaml
---
output:
  pdf_document: 
    latex_engine: lualatex 
documentclass: ltjsarticle 
---
```

#グラフに日本語が含まれる場合

[トライフィールズ: Ubuntu,R knitrで日本語のPDFを出力するための設定](http://www.trifields.jp/how-to-set-up-for-outputting-a-pdf-of-the-japanese-at-knitr-in-ubuntu-1404-and-r-1615)を参照


#.Rmdファイルの文字コード

SJISで特に問題はない。
UTF-8(BOMなし)も使える。
UTF-8(BOMあり)はYAMLを正常に読み込まなくなる。

#レンダリング

```r
require(rmarkdown)
render("test.Rmd", output_format = "pdf_document")
```

とすれば、"test.pdf"ファイルが作成される。














