#Linux Mintって？

Ubuntu派生のディストロで、使いやすくてUIもよい。
Unityなんてなかった！！（本家Ubuntuでも18からGnomeに戻るらしい）

別にRedHat系でもOpenSuse系でもよいのだけれど、Linuxにすると、**RStudioServer**が使えて、色んな端末から同じ環境で計算ができるようになる。
やったネ！

ホントはDockerコンテナとか使うともっといいらしいのだけれど、こういうのはちょっとずつね。

#今回の動作環境
第7世代Intel CPUにLinux Mint 18.1

#RとRStudioServerのインストール

[ここ](https://www.rstudio.com/products/rstudio/download-server/)に書いてある通りにするだけ。
できたら[http://localhost:8787/](http://localhost:8787/)にアクセスしてみよう

英語は嫌だという人のために必要部をコピペしておくと

64bit版のLinux MintやUbuntu,Debianなら

```
$ sudo apt-get install r-base
$ sudo apt-get install gdebi-core
$ wget https://download2.rstudio.org/rstudio-server-1.0.143-amd64.deb
$ sudo gdebi rstudio-server-1.0.143-amd64.deb
```

を実行するだけ簡単！


#`needs`の導入

`needs`パッケージは複数パッケージの読み込みを容易にしつつ、存在しないパッケージを自動でインストールしてくれる優れもの。
詳しい紹介は[uriさん](http://qiita.com/uri/items/dd7c5cddbb6b0ae342c6)の記事を参照。

導入手順として[githubのページ](https://github.com/joshkatz/needs)には

```
install.packages("needs")
# for the dev version:
# devtools::install_github("joshkatz/needs", ref = "development")
library(needs)

# answer "yes" when prompted, and you will never have
# to type library or install.packages again. hooray.
```

と書かれているが、ターミナルに`$ R`と入力してRを立ち上げ、上記を実行しても失敗する。
エラーメッセージのコピーを忘れてしまって申し訳ないが、書き込み制限のある設定ファイルをいじるのが原因のようなので、`$sudo R`としてRをスーパーユーザーで立ち上げてやればよい。

#`tidyverse`の導入

みんな大好きtidyverseだが、いかんせん多数のパッケージの集合体であるために、依存関係が複雑なようだ。
R内でのパッケージの依存関係はもちろんのこと、OS側のソフトの依存関係にも注意が必要。
今回の環境では以下の順でコードを実行すると、うまく`tidyverse`を導入できた。

```
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install build-essential gfortran g++ libcurl4-openssl-dev libxml2-dev
$ R
> install.packages("readxl")
> install.packages("tidyverse")
```

上述の`needs`を早速使って"tidyverse"をインストールしたいところだが、`install.packages`のほうが、インストール過程の詳細なメッセージが見られるので、今回は後者を推す。

うまくいかなかった場合はメッセージを確認しよう。

RStudioServerをインストールした人は、Rコード部分はRStudioServer上でやってもいい。

```
/bin/bash: g++: command not found
```

のようなメッセージが出てきた場合は、システム側に不足なソフトウェアがある（上述の場合は`g++`)
大体の`tidyverse`インストール周りのトラブルはこのエラーに起因している。
一通りシステムに必要なソフトをインストールしたら、もう一度、`tidyverse`をインストールしてみよう。


```
Error in library.dynam(lib, package, package.lib) : 
  shared object ‘readxl.so’ not found
```

というようなメッセージがあれば、個別にRのパッケージをインストールしてみるといい（上述の場合は`readxl`）。

これで、だいたいRを快適に利用できるようになったはずだ。

#その他のパッケージの導入

このあたりはお好みで。

```
needs(data.table, pipeR)
```

`data.table`は、data.frameの拡張するもの。
他に、表形式ファイルを`base::read.csv`などよりも高速に読み込める`fread`や、`base::write.csv`などよりも高速に書き込める`fwrite`などの関数が用意されている。

`pipeR`はRでパイプラインを可能にするパッケージ。
`magrittr`とライバル関係（？）にあたるが、パフォーマンスなどの観点で`pipeR`の方が優れているらしい。



