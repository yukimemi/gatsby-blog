---
title: Use neovim in docker
date: "2018-10-30T00:16:06+09:00"
layout: post
draft: false
category: "docker"
tags:
- "neovim"
- "docker"
description:
  Docker の中で neovim を起動してやれば、どんな環境でも neovim が使えていいのではないか？と思い、 Dockerfile を書いてみた。
---

Docker の中で neovim を起動してやれば、どんな環境でも neovim が使えていいのではないか？と思い、 Dockerfile を書いてみた。

同じような事をやろうとしている人はやっぱりいるみたいで、参考となる URL があった。

[alpine で設定込み Neovim Docker イメージのサイズを半減させた - Qiita](https://qiita.com/succi0303/items/4fdb2a9ff2b3e069448b)

これに、 docker 内とホスト OS との権限の問題を回避する為に、ビルド時にホスト OS と同一の id をもつユーザー作成を行う。

`Dockerfile` はこんな感じ。

```Dockerfile
FROM alpine:edge
MAINTAINER yukimemi

ARG USERID=1000
ARG GROUPID=1000
ARG USERNAME=yukimemi
ARG GROUPNAME=staff
ARG HOMEPATH=/Users/yukimemi

RUN echo "http://dl-4.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories

RUN apk update && \
    apk upgrade && \
    apk add --no-cache \
    shadow \
    curl \
    gcc \
    git \
    linux-headers \
    musl-dev\
    neovim \
    python-dev \
    py-pip \
    python3-dev \
    py3-pip && \
    rm -rf /var/cache/apk/*

ENV LANG="ja_JP.UTF-8" LANGUAGE="ja_JP:ja" LC_ALL="ja_JP.UTF-8"

RUN pip3 install --upgrade pip neovim
RUN mkdir -p ${HOMEPATH}

COPY .config ${HOMEPATH}/.config

RUN groupadd -g ${GROUPID} ${GROUPNAME}; exit 0
RUN useradd -u ${USERID} -g ${GROUPID} -d ${HOMEPATH} -s /bin/bash ${USERNAME}
RUN chown -R ${USERID}:${GROUPID} ${HOMEPATH}

USER ${USERNAME}
WORKDIR ${HOMEPATH}

RUN nvim +qa

ENTRYPOINT ["nvim"]
```

plugin manager は [k-takata/minpac](https://github.com/k-takata/minpac) を使っており、 plugin がインストールされていない場合は自動で plugin をインストールするようにしている。

上記の Dockerfile をビルドする時は、以下のようなコマンドラインで実施する。

```sh
> docker build --tag yukimemi/neovim --build-arg USERID=$(id -u) --build-arg USERNAME=$(id -un) --build-arg GROUPID=$(id -g) --build-arg GROUPNAME=$(id -gn) --build-arg HOMEPATH=${HOME} .
```

自分は dotfiles の中にこの Dockerfile を入れているので、他の実行したいタスクと一緒に `Makefile` を作ってビルドしている。

[こんな](https://github.com/yukimemi/dotfiles/blob/master/Makefile) 感じ。

なかなか便利。

実際に使用する時はマウントやらなんやらやらないかんから、以下のように呼び出す。
backupdir の設定とか plugin の設定とかによってはマウント元と先が変わるかも。

`fish` の場合。

```fish
> docker run --rm -it -v $PWD:$PWD -v $HOME/.cache/ctrlp:$HOME/.cache/ctrlp -v $HOME/.cache/neosnippet:$HOME/.cache/neosnippet -v $HOME/.cache/nvim/back:$HOME/.cache/nvim/back -v $HOME/.local/share/nvim:$HOME/.local/share/nvim -w $PWD yukimemi/neovim $argv
```

べんり。

---

### 参考

[alpine で設定込み Neovim Docker イメージのサイズを半減させた - Qiita](https://qiita.com/succi0303/items/4fdb2a9ff2b3e069448b)
