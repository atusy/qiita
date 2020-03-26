
本記事でやりたいことはCtrl+Shift+Lで賄えることを教えてもらいました。
これによりexportされない関数も読み込むことができます。
(2018/06/08 18:00)

<blockquote class="twitter-tweet" data-lang="en"><p lang="ja" dir="ltr">あれ、Load allはだめなの？（Ctrl+Shift+L）</p>&mdash; Hiroaki Yutani (@yutannihilation) <a href="https://twitter.com/yutannihilation/status/1004943801155182592?ref_src=twsrc%5Etfw">June 8, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


----------

ひょっとしたら便利なTip?

パッケージ製作にデバグはつきものですが、そのためのオブジェクト(関数など)の読み込みが面倒です。
多分、よくある手は

- RStudio上で、開いているソースコードを適宜source ( `Ctrl+Shift+S` )
- パッケージをビルド&ロード ( `Ctrl+Shift+B` )

のどちらかでしょう。
前者であれば、exportされていない関数も使えますが、後者であれば、exportされていない関数を使うには `package:::function` する必要があり面倒です。

そこで、以下のコードを.Rprofileに仕込んでみました。

これにより、プロジェクトを開いた時、プロジェクトがパッケージ作成を目的としていると `./R`以下にあるソースファイル(*.R)を全て `source` してくれます。

```r
rproj <- dir(pattern = '\\.Rproj$')[1]
if(!is.na(rproj) && any(grepl('BuildType: Package', readLines(rproj)))) {
  invisible(lapply(
    dir('./R', pattern = '*.R', full.names = TRUE),
    source
  ))
}
rm(rpoj)
```

関数を読み込み直したい時はRを再起動しましょう ( `Ctrl+Shift+R` )。

注意点として、オブジェクトが全てGlobal環境にassignされてしまいます。

開発中のパッケージを `library(package)` してもそれらはGlobal環境下にあるものにマスクされてしまいます。
適宜 `rm` して下さい。

Enjoy!

