#こんなデータフレームを

| int|lgl  |chr |lgl.1 |lgl.2 | int.1|chr.1 |lgl.3 |chr.2 |chr.3 |
|---:|:----|:---|:-----|:-----|-----:|:-----|:-----|:-----|:-----|
|   1|TRUE |chr |TRUE  |TRUE  |     1|chr   |TRUE  |chr   |chr   |
|   1|TRUE |chr |TRUE  |TRUE  |     1|chr   |TRUE  |chr   |chr   |
|   1|TRUE |chr |TRUE  |TRUE  |     1|chr   |TRUE  |chr   |chr   |
|   1|TRUE |chr |TRUE  |TRUE  |     1|chr   |TRUE  |chr   |chr   |
|   1|TRUE |chr |TRUE  |TRUE  |     1|chr   |TRUE  |chr   |chr   |

#こうしたい

|chr |chr.1 |lgl  |lgl.1 |lgl.2 |lgl.3 | int| int.1| int.2| int.3|
|:---|:-----|:----|:-----|:-----|:-----|---:|-----:|-----:|-----:|
|chr |chr   |TRUE |TRUE  |TRUE  |TRUE  |   1|     1|     1|     1|
|chr |chr   |TRUE |TRUE  |TRUE  |TRUE  |   1|     1|     1|     1|
|chr |chr   |TRUE |TRUE  |TRUE  |TRUE  |   1|     1|     1|     1|
|chr |chr   |TRUE |TRUE  |TRUE  |TRUE  |   1|     1|     1|     1|
|chr |chr   |TRUE |TRUE  |TRUE  |TRUE  |   1|     1|     1|     1|

#テストデータの用意

```r

nr <- 5 #テストデータ(data.frame)の行数

d0 <- data.frame( #テストデータの基本構成要素
  int = rep(1, nr), #integer
  chr = rep('chr', nr), #character
  lgl = rep(TRUE, nr), #logical
  stringsAsFactors = FALSE #characterがfactorに変換されることを防ぐ
)
set.seed(123) #乱数の固定
d1 <- d0[sample.int(length(d0), 10, replace = TRUE)] #d0を基に10列のデータフレームを作成

d1 #テストデータ
```

| int|lgl  |chr |lgl.1 |lgl.2 | int.1|chr.1 |lgl.3 |chr.2 |chr.3 |
|---:|:----|:---|:-----|:-----|-----:|:-----|:-----|:-----|:-----|
|   1|TRUE |chr |TRUE  |TRUE  |     1|chr   |TRUE  |chr   |chr   |
|   1|TRUE |chr |TRUE  |TRUE  |     1|chr   |TRUE  |chr   |chr   |
|   1|TRUE |chr |TRUE  |TRUE  |     1|chr   |TRUE  |chr   |chr   |
|   1|TRUE |chr |TRUE  |TRUE  |     1|chr   |TRUE  |chr   |chr   |
|   1|TRUE |chr |TRUE  |TRUE  |     1|chr   |TRUE  |chr   |chr   |


#列の型順にソート

```r

classes <- sapply(d1, class) #各列の型を入手
d2 <- d1[order(classes)] #ソート

d2
```


|chr |chr.1 |lgl  |lgl.1 |lgl.2 |lgl.3 | int| int.1| int.2| int.3|
|:---|:-----|:----|:-----|:-----|:-----|---:|-----:|-----:|-----:|
|chr |chr   |TRUE |TRUE  |TRUE  |TRUE  |   1|     1|     1|     1|
|chr |chr   |TRUE |TRUE  |TRUE  |TRUE  |   1|     1|     1|     1|
|chr |chr   |TRUE |TRUE  |TRUE  |TRUE  |   1|     1|     1|     1|
|chr |chr   |TRUE |TRUE  |TRUE  |TRUE  |   1|     1|     1|     1|
|chr |chr   |TRUE |TRUE  |TRUE  |TRUE  |   1|     1|     1|     1|


パイプラインでやるなら

```r
library(dplyr)

d1 %>%
  `[`(order(sapply(., class)))


```

