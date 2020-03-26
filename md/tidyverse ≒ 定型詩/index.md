tidyverseってなんぞ?

このところのtidyverseユーザーたちの関心で，[Advent Calendar](https://adventar.org/calendars/2188)にまでなってしまいました．
そこで私も12/9のAdvent Calendarに登録しました．
先だっては[俺たちのtidyverseはこれからだ！ by yutanihilationさん](http://notchained.hatenablog.com/entry/tidyverse)やTwitter上で話題になりました．
話題を取り上げた方々が訳語を知りたいのではなく，実態に迫りたいのだろうことは想像にかたくありません．
そして「[もうtidyの訳語はtidyでよくない？ by yutanihilationさん](http://notchained.hatenablog.com/entry/2017/10/26/203205)」といった事態に．

こんなに話題になるのは，tidyverseが発展途上ながら，実用的だからでしょう．
加えて，tidy data([整然データってなに？ by Nishiharaさん](https://speakerdeck.com/fnshr/zheng-ran-detatutenani))とtidyverseは等価ではない(tidyverseにtidy dataは含まれる包含関係？)ことが，混乱を招きます．

で，tidyverseとはなにかを考える時，みなさんtidyに注目しがちですが，verseに注目してみました．

universe(宇宙)やmultiverse(多元的宇宙)という言葉があることから，tidyvereはtidyな世界とか宇宙とか環境とかそういった意味合いだろうと皆さん念頭に置いている気がします．
しかし，verseの訳語を英辞郎で調べると

- (動) 〔散文を〕韻文に作り替える
- (名) 〔ある形式の〕詩◆無韻詩（blank verse）や自由詩（free verse）などがある。

といった説明が出てきます．
ということは

tidyverse == tidy詩 == tidyポエム？!

なんということでしょう．
我々のAdvent Calendarもtidyverseなのです．

さて，ここでもう一つ重要なのが，動詞としてのverseで"韻文に作り替える"です．
もうお分かりですよね．
みなさん大好きパイプ演算子で韻を踏もうということです．

```r:散文
plot(density(iris$Sepal.Length))
```

```r:韻文
iris %>%
  dplyr::select(Sepal.Length) %>%
  unlist %>%
  density %>%
  plot
```

更に[The tidy tools manifesto](https://cran.r-project.org/web/packages/tidyverse/vignettes/manifesto.html)にあるルールを足して，詩としての体裁を整えて(tidy)いく．
これは詩でいう自由詩(free verse)より定型詩(rythmed verse)でいこうぜ!といったムーブメントに他なりません!(過言)

ということで，

tidyverseは「詩を整える」という意味で，転じて定型詩を訳語にするというのはいかがでしょうか．

皆さん詩うようにcodingしましょう!!

いいかげん，ggplot2もパイプを基本にしてもらいたいものです．

