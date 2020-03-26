LinuxでSambaを用いてファイル共有する時、共有フォルダ内にシンボリックリンクを作っても既定ではアクセスできない。

Ubuntu 10.4以降では/etc/samba/smb.confに

```
[global]
unix extensions = no
wide links = yes
```

を追記すればいいようだ([それマグで!](http://takuya-1st.hatenablog.jp/entry/20100509/1273424397))。

ReadyNASの場合、/etc/samba/smb.confを覗いてみると編集するなと書いてある。
自動生成なので、せっかく書き換えても上書きされてしまうようだ。
もう少し中を見ると、

```
include = /etc/frontview/samba/smb.conf.defaults
include = /etc/frontview/samba/smb.conf.%a
include = /etc/frontview/samba/smb.conf.overrides
include = /etc/frontview/samba/Shares.conf
include = /etc/frontview/samba/Shares.conf.%G
include = /etc/frontview/samba/addons/addons.conf
include = /run/usb/samba/Shares.conf
```

と複数のconfファイルを参照しているので、いずれかに先の内容を追記すればいい。
私は

```
include = /etc/frontview/samba/smb.conf.defaults
```

を選んだ。

ただし、ReadyOSのアップデートの度に、confファイルは修正されてしまうようで、毎度修正の必要がある。



