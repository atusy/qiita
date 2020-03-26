.Rprofileで起動時に素のRかRStudioどちらが起動されたか判定したいのですが、うまくいっていません。
どなたかうまい解決方法ご存知でしたら一報下さい。

Rでは、実行環境を、

```r
.Platform$GUI
```
で知ることができます。

Windowsで素のRを起動すると、RguiないしRtermが、
Linuxで素のRを起動すると、X11が、
WindowsでもLinuxでもRStudioを起動すると、RStudioが返ります。

基本的には……!

これを利用すると、素のRの時とRStudioの時で.Rprofileを使い分けることが可能なのではないかと期待したのですが、実はそうはなりません。

.Rprofileに

```r
.Platform$GUI
```

を書いてRStudioを起動すると、

```
[1] "X11" #私はLinuxユーザーです
```

と返ります。
残念!

一度、素のRを呼んでいるのでしょうか？
ちょっとどうしてこうなっているのか分かりません。



