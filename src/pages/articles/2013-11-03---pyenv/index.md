---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- python
Description: ""
Tags:
- python
- pyenv
- rbenv
date: "2013-11-03"
title: pyenv python でも rbenv
---

一昔前は、python で仮想環境といえば、 virtualenv と
virtualenvwrapper だったっぽいけど、 今は rbenv
とほぼ同じ使い勝手の pyenv がおすすめっぽい。

名前も 「〜env」で一緒だしね。

### 簡単導入

以下を .zshenv に追記

```sh
# pyenv
[ ! -d $HOME/.pyenv ] && git clone git://github.com/yyuu/pyenv.git ~/.pyenv
if [ -d $HOME/.pyenv ]; then
    [ ! -d $HOME/.pyenv/plugins/pyenv-virtualenv ] && git clone git://github.com/yyuu/pyenv-virtualenv.git ~/.pyenv/plugins/pyenv-virtualenv
    export PYENV_ROOT="$HOME/.pyenv"
    export PATH=$PYENV_ROOT/bin:$PATH
    eval "$(pyenv init -)"
fi
```

zshを再起動すればインストール完了。 pyenv
使いたくないってなったら、1行目の git clone をコメントアウト。

### pyenv 使用方法

#### 1. python のインストール

```sh
$ pyenv install -l
Available versions:
2.4
2.4.1
2.4.2
2.4.3
2.4.4
2.4.5
2.4.6
2.5
2.5.1
・・・以下略
$ pyenv install 3.3.2
```

#### 2. virtualenv 環境の作成

```sh
$ pyenv virtualenv --distribute 3.3.2 sample_project
```

#### 3. 使用する環境の選択

```sh
$ pyenv global 3.3.2    # デフォルトの設定

$ cd sample_project
$ pyenv local sample_project    # ローカル環境での設定
```

ローカル環境で使うバージョンは、 sample\_project フォルダの
.python-version に記載されており、
グローバル環境で使うバージョンは、 \~/.pyenv/version
に記載されてるっぽい。

#### 参考:

[yyuu/pyenv](https://github.com/yyuu/pyenv)

[yyuu/pyenv-virtualenv](https://github.com/yyuu/pyenv-virtualenv)

[pyenv+virtualenv環境の作成方法まとめマン - 256bitの殺人メニュー](http://d.hatena.ne.jp/akuwano/20130818/1376833810)

[plenvとpyenvに乗り換えた -ぱせらんメモ](http://d.hatena.ne.jp/pasela/20130219/llenv)

