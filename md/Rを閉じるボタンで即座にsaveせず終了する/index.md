#結論

Rを閉じるボタンで即座にsaveせず終了するようにしたければ、.Rprofileに

`q <- function (save = "no", status = 0, runLast = TRUE) .Internal(quit(save, status, runLast))`

を追加しよう。

注意: 既存の関数の上書きはバグの温床になりかねないので自己責任でお願いします。

#Rを終了するには

-  閉じるボタン
-  `q()`

のどちらかを実行する。
この時セッションを保存するか聞かれるが、だいたい保存しない(と思う)。
聞かれたくない人は

`q(save = "no")`

を実行する必要がある。

または、「[R を終了させる最短コードがおもしろい](http://d.hatena.ne.jp/hoxo_m/20111026/p1)」でも紹介されているように、

`class(Q)=Q="no";print.no=q`

を.Rprofileに記入しておくと、`Q`一文字で終了できる。

#閉じるボタンでも即終了したいんじゃ！

という人は、

関数`q`の引数既定値を`save = "no"`にして上書きすればいい。

標準では
`q <- function (save = "default", status = 0, runLast = TRUE) .Internal(quit(save, status, runLast))`

となっているので、

`q <- function (save = "no", status = 0, runLast = TRUE) .Internal(quit(save, status, runLast))`

として、これを.Rprofileに書き込む。

既存の関数の上書きはバグの温床になりかねないので自己責任でお願いします。
最も、他の関数から`q`が呼び出されることなんて早々ないだろうから大丈夫だろうと思うのですが……。

ちなみに、`rm(q)`すればいつでも元の`q`(すなわち`base::q`)が使えるようになります。

これは上述の`class(Q)=Q="no";print.no=q`とも共存可能で、これでコンソールからでも閉じるボタンからでも即座にRを終了できるようになりました。

やったネ！













