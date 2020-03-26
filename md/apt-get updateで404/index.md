#発端

Ubuntu 14.04 LTSを16.04LTSにアップグレードしようと思ったが，

`sudo apt-get update`

で404エラー続出．
無視して以下を実行しようとするものの

```
sudo apt-get upgrade
sudo do-release-upgrade
sudo reboot
sudo do-release-upgrade -d
sudo reboot
```

404エラーのせいで`sudo do-release-upgrade`が止まる．

#ネット上で見かける解

http://qiita.com/ytyng/items/76784390a538bbb5117e
によると404エラーは古いバージョンのUbuntuを使っていると置きるらしく，

`/etc/apt/source.list`

に含まれるsecurity.ubuntu.comとarchive.ubuntu.comをold-release.ubuntu.comに置換すればいいとされる．
それは以下のコードで一発．

`sudo sed -i -e 's/archive.ubuntu.com\|security.ubuntu.com/old-releases.ubuntu.com/g' /etc/apt/sources.list`

しかし，やはり404エラーが出る．

Ubuntu 14.04 LTSの通称はtrustyだが，

http://old-releases.ubuntu.com/ubuntu/dists/

を見ると，trustyが含まれていない．
つまり，trustyはまだold-releaseではないらしいのだ．

#結局どうするか

source.listを初期化してみたところうまくいった．

```
sudo rm /etc/apt/sources.list
sudo -i software-properties-gtk
```

を実行した後，適宜レポジトリを選択し直す．
今回は
https://askubuntu.com/questions/124017/how-do-i-restore-the-default-repositories
を見ながらGUIで行った．

これで


```
sudo apt-get update
sudo apt-get upgrade
sudo do-release-upgrade
sudo reboot
sudo do-release-upgrade -d
sudo reboot
```

すれば無事更新されるはずだ．

#おまけ: \bootの空き容量が足りない!

無事に行くと思ったらまた`sudo do-release-upgrade`で躓いた．
今度は`\boot`の空き領域が足りないらしい．
これは古いバージョンのカーネルを削除すればOK

`dpkg -l linux-image-\* | grep ^ii`

でインストールされているカーネルの一覧を出し，古いものを削除する

`apt-get purge linux-image-hogehoge`

