[オンプレでVS Code as a Serviceできるcode-serverを試す](https://qiita.com/m5d215/items/f5ae82ad9a3726519904) を読んでついに来た! となった．
Github の [codercom/code-server](https://github.com/codercom/code-server) を見ると，以下のコマンドにより `docker` でも試せるらしい．
せっかくなので `docker-compose` してみた．
今のところ `root` ユーザでしかコーディングできないっぽいので，強い人による `Dockerfile` 更新が待たれる．
`environment` で任意のユーザーを作れるようになったら実用しようかな．

# docker で code-server

公式に載ってる． `code-server --allow-http --no-auth` をつけてると少なくとも Firefox ではセキュリティ的にアウトで弾かれる．

```sh
docker run -p 127.0.0.1:8443:8443 -v "${PWD}:/root/project" codercom/code-server code-server --allow-http --no-auth
```

# docker-compose で code-server

以下の `docker-compose.yaml` を作成後， `sudo docker-compose up` して，ログに出てくるパスワードを入力 (よって `-d` オプション不可)．
二度目以降の起動時は `-d` をつけても OK．
`--allow-http --no-auth` はコメントアウトした．

永続化により，projectディレクトリ以下はホストに保存されるが，ホストによる編集にはルート権限が必要．

```{docker-compose.yaml}
version: "3"
services:
  code:
    image: codercom/code-server
    restart: always
    ports: 
      - "8443:8443"
    # command: code-server --no-auth --allow-http
    volumes:
      - ./project:/root/project
```

