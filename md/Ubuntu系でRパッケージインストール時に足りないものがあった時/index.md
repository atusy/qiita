CRAN上のRパッケージはWindows向けにはバイナリで提供されていることがほとんどですが、Linux向けにはソースで提供され、コンパイルが必要です。
このため、`install.packages('hoge')`すると、

```
configure: error: "ODBC headers sql.h and sqlext.h not found"
```

といったメッセージが返ることがあります。

ほうほうODBCね、と思ってbashで

```bash
apt search odbc
```

するとうんざりするほどのodbcを名前に含むパッケージが!

経験豊かな人でなければ、どれを入れればいいか分かりません。

# grepする

例えば、開発者向けパッケージが怪しいなら

```bash
apt search odbc | grep dev
```

として、grepで絞り込むことができます。

`|`はパイプで、左側の結果を右側のコマンドに渡してくれます。
この場合、左側の結果の中から、grepでdevを含むパッケージを絞り込みます。

バージョン(新旧)やアーキテクチャ(x64 or i836)の違いを除くと以下にまで絞れます。

- libghc-haskelldb-hdbc-odbc-dev - HaskellDB support for the HDBC ODBC driver
- libghc-hdbc-odbc-dev - unixODBC HDBC (Haskell Database Connectivity) Driver for GHC
- libiodbc2-dev - iODBC Driver Manager (development files)
- libocamlodbc-ocaml-dev - UnixODBC database bindings for OCaml
- unixodbc-dev - ODBC libraries for UNIX (development files)                                                    

なんとなくunixodbc-devがprimitiveっぽくて怪しい気がしますが自身は持てません。
結局sql.hとsqlext.hを含んでいないものをインストールしてしまうと残念。

# apt-file findする

apt-file findであるファイルを含むパッケージがどれか調べることができます。
これを用いてsql.hとsqlext.hを含むパッケージを探しましょう。

apt-fileが未インストールであればインストールします。

```bash
sudo apt update
sudo apt install apt-file
```

apt-fileを使いましょう

```bash
apt-file update
apt-file find ^sqlext.h$
```

まずupdateすることでファイル一覧を入手します。
その後findで欲しいファイルを含むパッケージを見つけます。

manには1つのファイルを含むパッケージを見つけるとあるので

```
apt-file find sql.h sqlext.h
```

としても両方含むものを見つけることはできません。
うまく結果を統合したいですが私のshell力が足りない。

sqlext.hを含むパッケージは

- libiodbc2-dev: /usr/include/iodbc/sqlext.h
- libwine-development-dev: /usr/include/wine-development/windows/sqlext.h
- mingw-w64-common: /usr/share/mingw-w64/include/sqlext.h
- mingw-w64-x86-64-dev: /usr/x86_64-w64-mingw32/include/sqlext.h
- unixodbc-dev: /usr/include/sqlext.h
- wine1.6-dev: /usr/include/wine/windows/sqlext.h

ですので、やはりunixodbc-devでよさそうです。

```
apt install unixodbc-dev
```

すればsql.hとsqlext.hが手に入ります。

# Enjoy!!


# 追記: cran2debとかc2d4uの依存関係を見る

2018/03/30 16:23

yutannihilationさんに教えて頂きました。
ちょっとまだ試せていませんが、取り急ぎ。

<blockquote class="twitter-tweet" data-lang="en"><p lang="ja" dir="ltr">知ってるかもですが、cran2debとかc2d4uの依存関係を見るのが確実です。 <a href="https://t.co/Cy9ibZ2nWA">https://t.co/Cy9ibZ2nWA</a> あとは、あんまり使ったことないけど <a href="https://t.co/4wqwyeYMXU">https://t.co/4wqwyeYMXU</a> も便利かも。</p>&mdash; Hiroaki Yutani (@yutannihilation) <a href="https://twitter.com/yutannihilation/status/979595083661025280?ref_src=twsrc%5Etfw">March 30, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>






