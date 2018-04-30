---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- golang
Description: ""
Tags:
- golang
- direnv
- npm
date: "2014-07-02"
title: direnv for npm local commands
---

通常、 npm で実行コマンドをインストールするときは、以下のようにグローバルオプションを
つけてインストールすることが多いと思う。

```sh
$ npm install -g gulp
```

だけど、これではプロジェクトごとに違うバージョンを使いたい！などの場合にこまる。
そこで [direnv](https://github.com/zimbatm/direnv) 。

これを使うとあるディレクトリでは、「〜〜する」というのが簡単に出来る。

### 導入方法

まず、 [direnv](https://github.com/zimbatm/direnv) はgoを使うので、goをインストールしておく。

```sh
$ brew install go --cross-compile-common
```

以下を `.zshenv` に記載。

```sh
# direnv
if which go > /dev/null; then
    if [ ! -d $HOME/.direnv ]; then
        git clone https://github.com/zimbatm/direnv ~/.direnv
        pushd ~/.direnv
        make install
        popd
    fi
    eval "$(direnv hook zsh)"
fi
```

あとはシェルを再起動したらインストール完了。

### 使い方

プロジェクトのディレクトリでローカルにnpmのコマンドをインストールする。

```sh
$ npm install gulp
```

ローカルにインストールした場合、通常以下の場所に実行コマンドが配置される。

```sh
$ ls node_modules/.bin
bower  cake  coffee  gulp  nodemon  nodewebkit  wiredep
```

なので、この場所をPATHに追加すればよい。
[direnv](https://github.com/zimbatm/direnv) では、以下のようにする。

```sh
$ direnv edit .
```

これで、エディタで `.envrc` というファイルが開くので、以下の行を記載。

```sh
export PATH=$PWD/node_modules/.bin:$PATH
```

これでOK。こうすると、このディレクトリに来た場合は自動的にPATHに追加され、
反対にディレクトリから出た場合、自動的にPATHから削除される。

```sh
$ cd ~/.ghq/github.com/yukimemi/hogehoge
direnv: loading .envrc
direnv: export ~PATH
```

```sh
$ cd
direnv: unloading
```

これで、プロジェクトローカルなコマンドをかんたんに実行出来る。

- - -

##### 参考:

[direnvを使おう - Qiita](http://qiita.com/kompiro/items/5fc46089247a56243a62)

[direnvで解決するGOPATHの3つの問題点 - None is None is None](http://doloopwhile.hatenablog.com/entry/2014/06/18/010449)

[Big Sky :: bundle exec がウザい](http://mattn.kaoriya.net/software/lang/ruby/20140314032519.htm)

