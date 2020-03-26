#概要
data.table::freadは行が観測値、列が変数な表を読み込む関数
行が変数で列が観測値な表をfreadすると残念な結果になるから工夫しよう

#Rで表形式のファイル(csvなど)を読み込む一般的な方法
- read.table
- readr::read_delim
- readxl::readxlsx
- data.table::fread
- など

これらはいずれも、行に観測値を記録したファイルの読み込みしか対応していない。
例えばiris[1:5, ]のような表。

| | Sepal.Length | Sepal.Width | Petal.Length | Petal.Width | Species |
|:-----:|:-----:|:-----:|:-----:|:-----:|:------:|
|1 | 5.1 | 3.5 | 1.4 | 0.2 | setosa |
|2 | 4.9 | 3.0 | 1.4 | 0.2 | setosa |
|3 | 4.7 | 3.2 | 1.3 | 0.2 | setosa |
|4 | 4.6 | 3.1 | 1.5 | 0.2 | setosa |
|5 | 5.0 | 3.6 | 1.4 | 0.2 | setosa |

しかし、以下のように列に観測値を記録していく業界もあるのだ……。

| | 1 | 2 | 3 | 4 | 5 | 
|:-:|:-:|:-:|:-:|:-:|:-:|
|Sepal.Length | 5.1 | 4.9 | 4.7 | 4.6 | 5.0 | 
|Sepal.Width | 3.5 | 3.0 | 3.2 | 3.1 | 3.6 | 
|Petal.Length | 1.4 | 1.4 | 1.3 | 1.5 | 1.4 | 
|Petal.Width | 0.2 | 0.2 | 0.2 | 0.2 | 0.2 | 
|Species | setosa | setosa | setosa | setosa | setosa |

後者をcsvファイルとして、例えばdata.table::freadで読み込むと以下の問題が起こる

- data.tableはrow.namesを持たないので、項目(例えばSepal.Length)が観測値扱いになる
- 各列の型がcharacterになってしまい、plotなどで扱い辛い
    - data.tableはlist of columnsなので、型は列ごとに一意でなければならない。従って、列中に含まれる値を最も柔軟に表現できる型が選ばれる。
- 無論、読み込んだデータをtransposeしても、理想的に成形された表形式データを得られない
- 他にも細かい面倒が色々。

> \#今回は表の読み込みにdata.tableパッケージを使う
> require(data.table)
> 
> \#iris[1:5, ]を元にcsvファイルを用意
> csv <- "Sepal.Length,5.1,4.9,4.7,4.6,5.0\nSepal.Width,3.5,3.0,3.2,3.1,3.6\nPetal.Length,1.4,1.4,1.3,1.5,1.4\nPetal.Width,0.2,0.2,0.2,0.2,0.2\nSpecies,setosa,setosa,setosa,setosa,setosa\n"
> 
> \#csvファイルを読み込み
> d <- fread(csv)
> 
> \#各列の型はcharacterにcoercedされている
> sapply(d, typeof)
>>     V1          V2          V3          V4          V5          V6 
>> "character" "character" "character" "character" "character" "character" 
> 
> \#transposeしてみると
> (現実 <- t(d))
>>    [,1]           [,2]          [,3]           [,4]          [,5]     
>> V1 "Sepal.Length" "Sepal.Width" "Petal.Length" "Petal.Width" "Species"
>> V2 "5.1"          "3.5"         "1.4"          "0.2"         "setosa" 
>> V3 "4.9"          "3.0"         "1.4"          "0.2"         "setosa" 
>> V4 "4.7"          "3.2"         "1.3"          "0.2"         "setosa" 
>> V5 "4.6"          "3.1"         "1.5"          "0.2"         "setosa" 
>> V6 "5.0"          "3.6"         "1.4"          "0.2"         "setosa" 
> 
> (理想 <- data.table::as.data.table(iris[1:5, ]))
>>    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
>> 1:          5.1         3.5          1.4         0.2  setosa
>> 2:          4.9         3.0          1.4         0.2  setosa
>> 3:          4.7         3.2          1.3         0.2  setosa
>> 4:          4.6         3.1          1.5         0.2  setosa
>> 5:          5.0         3.6          1.4         0.2  setosa
>
> 現実 == 理想
>> FALSE

現実と理想の差を悔み、本論から目を反らした話をすると、Rは日本語を変数にできる。

#どうする？
- 毎回、元データをtransposeしてcsvに保存
- R上でデータを整形する関数を作る

前者は面倒なので後者を試みる。
##雛形
1. データをheaderなしcharacter型のdata.tableとして読込
2. 各列中の文字列をコンマ区切りの文字列として結合
3. 2の各項を改行文字(\n)で結合
4. freadし直す(headerアリ)

> require(data.table)
> require(pipeR)
> \#transpose fread
> tfread <- function(input) {
>     input %>>%
>         data.table::fread(header = FALSE) %>>%
>         as.matrix %>>% 
>         apply(2, paste, collapse = ",") %>>%
>         paste(collapse = "\n") %>>%
>         data.table::fread(header = TRUE)


##更に便利に
- 2回のfreadに好きなargumentを与えたい
- 表計算ソフト上のエラーメッセージ(e.g., #DIV/0!)を欠損値にしたい
    - 放置すると、エラーメッセージを含む変数の型はcharacter型になってしまう
- 空白の行・列を削除して、すっきりさせたい

###うおりゃあああ

> require(data.table)
> require(stringr)
>
> tfread <- function(input, remove_null = TRUE, ss = c('excel', 'libre'), args1 = list(), args2 = list()) {
> 	\#ABOUT ARGUMENTS\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#
> 	\#remove_null = TRUE removes null rows and columns
> 	\#ss: spread sheet
> 	\#args1: list of arguments to fread input data  
> 	\#args2: list of arguments to fread the transposed input data
> 	\#When there are duplicated arguments in args1 and args2, the prior argument has priority
> 	\#i.e., args1 = list(a = 1, a = 2, b = 3) is equivalent to args1 = list(a = 1, b = 3)
> 
> 	
> 	\#read table as character matrix\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#
> 	\#complete arguments
> 	args1 <- c(args1, input = input, header = FALSE, colClasses = 'character')
> 	\#remove duplicated arguments
> 	args1 <- args1[!duplicated(names(args1))]
> 	\#1st fread
> 	d <- as.matrix(do.call(data.table::fread, args1))
> 	rm(args1)
> 	
> 	\#replace error messages to blank\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#
> 	if(stringr::str_detect(ss[1], '^(excel|libre)$')) {
> 		d <- matrix(stringr::str_replace_all(d, '^\#.*\\!$', ''), nrow = nrow(d), ncol = ncol(d))
> 	}
> 	
> 	\#whether to remove rows and cols with no data\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#
> 	if(remove_null){
> 		filled <- d != ''
> 		filled[is.na(filled)] <- FALSE
> 		rows <- rowSums(filled) != 0
> 		cols <- colSums(filled) != 0
> 	} else {
> 		rows <- rep(TRUE, nrow(d))
> 		cols <- rep(TRUE, ncol(d))
> 	}
> 
> 	\#transpose data\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#
> 	args2 <- c(input = paste(apply(d[rows, cols], 2, paste, collapse = '\t'), collapse = '\n'),  args2, header = TRUE)
> 	args2 <- args2[!duplicated(names(args2))]
> 	do.call(data.table::fread, args2)
> }
> 

ところでインデントのしかたがよくわからない。

#なにはともあれEnjoy!

