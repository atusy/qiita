#rmarkdownとは

Rでデータ解析した結果(プロットなど)に、その説明や解釈をドキュメントとして加えたhtml, pdf, docxなどを出力できるスゴい奴。

詳しい説明は日本語でもいっぱいあります([kazutanさんのとか](https://kazutan.github.io/kazutanR/Rmd_intro.html#r_markdownとは))

#レポートが長くなった時に

ファイルを分割したくなりますよね。

そんなあなたに`child`引数。

\```{r, child = 'ichiro.Rmd'}

\```

とすると、親のRmdファイルの任意の場所にichiro.Rmdを挿入できます。
childはmdファイルでもOK

#もっとレポートが長くなった時に

もっとファイルを分割したくなりますよね。


\```{r, child = 'ichiro.Rmd'}

\```

\```{r, child = 'jiro.Rmd'}

\```


みたいなコードを何度も繰り返すのか……？
ムダに複数行使うし、順番変えにくいし……。

英単語のchildは単数形ですが、引数のchildは一人に限定しません。

\```{r, child = c('ichiro.Rmd', 'jiro.Rmd', 'saburo.Rmd', 'shiro.Rmd')}

\```

なんてことが可能なのです。
これなら、`c()`の中でRmdの順番を変えるなり、足すなり引くなり自由自在。

直前のチャンクで`x <- dir(pattern = '\\.Rmd$')`なんてしえおけばディレクトリ内にあるRmdファイルを一辺に合体なんてことも可能。
これはbookdownを使うことでも可能(また[kazutanさんの説明参照](https://kazutan.github.io/JapanR2016/JapanR2016.html#/))なのだけれど、スライド作りには使えないんだよなあ。
revealjsを使いたいので、こんな方法を考えてみました。

