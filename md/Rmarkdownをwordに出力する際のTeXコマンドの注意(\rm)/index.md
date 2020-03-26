数式中で一部の文字をノンイタリック(ローマン体)にしたい場合がある。

$a_\mathrm{a}$

この時使えるコマンドには

```TeX
\rm
```

$$a_\rm{a}$$

```TeX
\mathrm
```
$$a_\textrm{a}$$

```TeX
\textrm
```
$$a_\mathrm{a}$$

があるが、`\rm`は古く推奨されないらしい。
そのせいか、Rmdをhtmlやpdfに変換する時はいいが、wordに変換すると、数式にならない。
適宜`\mathrm`や`\textrm`を使おう。

