# %>%って？

dplyr(厳密にはmagrittr)というライブラリを導入することで使えるRでパイプラインを実現する中置演算子。

```r
plot(density(iris$Sepal.Length))
```

は、

「iris(アヤメ)のSepal.Length(萼片長)についてdensityでカーネル密度を計算して、結果をplotする」

に相当するが、頭で考える流れとコードの流れが見事に逆であることが分かる。

これが%>%を使うと

```r
library(dplyr)

iris$Species %>%
  density %>%
  plot
```

と思考と調和的な流れでコーディングでき、%>%でほどよく改行でき、とても読み易くなりデバグも用意になる。。

もう少し詳しいところはkilometerさんが先日のTokyo.Rで綺麗に図解してくださったので、[そちら](https://speakerdeck.com/kilometer/66th-tokyo-dot-r-beginner-session2?slide=16)を参照して頂きたい。

# hoge %>% returnで便利に

コーディングしていると

この処理をなくしたい!
処理の順番を変えたい!

といったことが往々にして起きる。
最終行以外はコメントアウトやカットアンドペーストで一発だ。

しかし、最終行が対象になると手間が増える。
たとえば、アヤメデータをフィルタリングするため

```r
iris %>% 
  filter(Sepal.Length > 5) %>% #萼片長が5以上
  filter(Species == 'setosa' %>% #setosa種
```

とした時、やっぱSpeciesはフィルタリングしなくていいとなると

1. 3行目を消す(orコメントアウトする)
2. 2行目の末尾のパイプラインを消す(orコメントアウトする)

といったステップを踏むことになる(以下)。

```r
iris %>% 
  filter(Sepal.Length > 5) # %>% #萼片長が5以上
  # filter(Species == 'setosa' %>% #setosa種
```

ところが、予めreturnで終わっておくと以下のように3行目をコメントアウトするだけでよい。

```r
iris %>% 
  filter(Sepal.Length > 5) %>% #萼片長が5以上
  # filter(Species == 'setosa' %>% #setosa種
  return()
```

3行目をコメントアウトするだけでよい。

RStudioユーザーであればCtrl + Shift + Cで行をコメントアウトできるので便利だ。
しかも適切にインデントしてくれる。

# hoge %>% str %>% returnで更に便利に

strを使うと、オブジェクトの構造が分かる。
デバグに便利。

```r
iris %>%
  select(-Species) %>%
  scale() %>%
  lm(Sepal.Length ~ ., data = .)
```

は失敗する。
scaleの返り値がマトリクスで、データフレームではないからだ。

```r
iris %>%
  select(-Species) %>%
  scale() %>%
  lm(Sepal.Length ~ ., data = .) %>%
  str() %>%
  return()
```

としておくと、

```r
iris %>%
  select(-Species) %>%
  scale() %>%
  # lm(Sepal.Length ~ ., data = .) %>%
  str() %>%
  return()
```

任意行をコメントアウトした時の結果の構造を見ることができる。
そして、あ、scaleの返り値はmatrixなのかと気付くと,`as.data.frame %>%`を挿入して`str %>%`をコメントアウトすればいい。

```r
iris %>%
  select(-Species) %>%
  scale() %>%
  as.data.frame %>%
  lm(Sepal.Length ~ ., data = .) %>%
  # str() %>%
  return()
```

この長さのパイプラインでは大したことがないが、長くなればなるほど便利だ。

```r
iris %>%
  select(-Species) %>%
  # scale() %>%
  # as.data.frame %>%
  # lm(Sepal.Length ~ ., data = .) %>%
  str() %>%
  return()
```

とほとんどをコメントアウトして、うまくいっていることを確認したら一行ずつコメントアウトを取り消し、strを見ることを繰り返して最後にstrをコメントアウトするといったデバグができる。

