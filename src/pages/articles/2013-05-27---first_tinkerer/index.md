---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- python
Description: ""
Tags:
- python
- tinkerer
date: "2013-05-27"
title: First Tinkerer
---

Tinkerer でブログ書く。

今まではgithubでocropressで書いてたけど、やっぱこれからは sphinx
でしょ！ markdown より reStructuredText でしょ！

で、 bitbucket を使う。

bitbucket では、 github と同じように、
\<ユーザー名\>.bitbucket.org で 自分のページを持つことが出来る。

### リポジトリの作成

bitbucket で、 \<ユーザー名\>.bitbucket.org
というリポジトリを作る。 僕の場合は、 yukimemi.bitbucket.org

### Tinkererのインストール

僕の場合は python3 ではダメだったので、 python2.7.4 で virtualenv
を作成して Tinkerer をインストールした。 python のインストールには
pythonz を使用している。

```sh
$ pythonz install 2.7.4
$ mkvirtualenv -p /Users/yukimemi/.pythonz/pythons/CPython-2.7.4/bin/python --distribute 2.7.4
$ workon 2.7.4
$ easy_install pip
$ pip install Tinkerer
```

### ブログの作成

bitbucketでgit repositoryを作成したら、それを clone する。

```sh
$ git clone ssh://git@bitbucket.org/yukimemi/yukimemi.bitbucket.org.git
$ cd yukimemi.bitbucket.org
```

clone した後は、 Tinkerer でブログを初期化する。

```sh
$ tinker -s
```

そして、 同ディレクトリに作成された、 conf.py を適当に修正する。

```python
#
# TODO: Edit the lines below
#

# Change this to the name of your blog
project = 'yukimemi blog'

# Change this to the tagline of your blog
tagline = ''

# Change this to the description of your blog
description = 'This is yukimemi\'s blog'

# Change this to your name
author = 'yukimemi'

# Change this to your copyright string
copyright = '1984, ' + author

# Change this to your blog root URL (required for RSS feed)
website = 'http://yukimemi.bitbucket.org/'

#
# More tweaks you can do
#

# Add your Disqus shortname to enable comments powered by Disqus
disqus_shortname = 'yukimemi'
```

こんな感じ。コメントシステムは Disqus
が採用されているので、事前にアカウント作成しておく必要あり。

ブログの作成は以下のコマンドで

```sh
$ tinker -p "First Tinkerer"
```

これで、

```
├── 2013
│ └── 05
│     └── 27
│         ├── first_tinkerer.rst
```

こんなふうに reST ファイルが出来るので、編集する。

```
$ vim 2013/05/27/first_tinkerer.rst
```

編集が終わったら、ビルドする。

```
$ tinker -b
```

実に簡単。 確認は index.html をブラウザで開けばおっけー。

問題なければ bitbucket に push

```
$ git add .
$ git commit -m "initial commit"
$ git push -u origin master
```

