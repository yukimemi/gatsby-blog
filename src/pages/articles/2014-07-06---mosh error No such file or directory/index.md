---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- mosh
Description: ""
Tags:
- mosh
- zsh
- ssh
date: "2014-07-06"
title: mosh error No such file or directory
---

mosh というmobile-shelllを導入してみたところ、「No such file or directory」という
エラーが発生して使えなかった・・・。

原因は結局よくわからなかったんだけど、とりあえず使えるようになったので、一応メモとして残しとこ。


### mosh install
環境は Mac OSX Mavericks。Homebrewで簡単にインストール出来た。

```sh
$ brew install mosh
```

昔は ```mobile-shelll``` だったみたいだけど、今は ```mosh``` でインストール出来る。
クライアント側とサーバ側両方インストールしておく必要あり。

### 使い方
普通の ```ssh``` を ```mosh``` に変えるだけ。

```sh
$ ssh yukimemi@yukimemi-my-host.com
```

↓

```sh
$ mosh yukimemi@yukimemi-my-host.com
```

だけど、なぜか、こんなエラーが出て使えなかった・・・。

```sh
$ mosh yukimemi@yukimemi-my-host.com
zsh: No such file or directory
ssh_exchange_identification: Connection closed by remote host
/usr/local/bin/mosh: Did not find remote IP address (is SSH ProxyCommand disabled?).
```

なんかzshが見つかんない？みたいなエラー。
zshが悪いのかと思い、bashからやってみてもエラーになった。

```sh
$ mosh yukimemi@yukimemi-my-host.com
bash: No such file or directory
ssh_exchange_identification: Connection closed by remote host
/usr/local/bin/mosh: Did not find remote IP address (is SSH ProxyCommand disabled?).
```

環境変数が悪いのか？と思い、以下のようにしてみた。

```sh
$ SHELL=/bin/bash mosh yukimemi@yukimemi-my-host.com
bash: mosh-server: command not found
Connection to yukimemi-my-host.com closed.
/usr/local/bin/mosh: Did not find mosh server startup message.
```

一応なんかエラーメッセージは変わったみたい。
これは ```mosh-server``` コマンドが見つかんないって言ってるだけなので、
moshのオプションで指定出来るっぽい。

```sh
$ SHELL=/bin/bash mosh --server=/usr/local/bin/mosh-server yukimemi@yukimemi-my-host.com
[yukimemi@yukimemi-my-host.com] $
```

つながった。
Airを閉じて一度ネットの接続がきれても、自動で再接続してくれるの便利。

- - -

##### 参考

[Moshを使ってみる #Linux - rcmdnk's blog](http://rcmdnk.github.io/blog/2014/06/30/computer-linux-mac/)

