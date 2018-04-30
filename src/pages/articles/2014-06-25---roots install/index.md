---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- roots
Description: ""
Tags:
- roots
- nodejs
- javascript
date: "2014-06-25"
title: roots install
---

今までブログはTinkererを使用していたけど、これからは、 roots で書くことにしよう。

roots は、静的サイトジェネレーター。nodeで動く。
特徴としては、以下の形式で作成出来ること。

* スクリプト -> coffeescript
* HTML -> jade
* CSS -> stylus

しかも、ライブリロードが標準で組み込まれているので、これが非常に便利。
jade ファイルを保存したら、自動でブラウザが更新され、すぐ確認出来る。

---

### ブログ作成手順

#### 1. npmでグローバルにインストール

```sh
$ npm install -g roots
```

#### 2. ブログテンプレートでプロジェクト作成

```sh
$ roots new yukimemi.github.io --blog
```
これでブログ完成。

#### 3. roots コマンドでコンパイル & ウォッチ

```sh
$ cd yukimemi.github.io
$ roots watch
```

自動的にコンパイルが動き、 public ディレクトリにコンパイル結果が入り、さらにport 1111でサーバ
を起動してくれ、さらにデフォルトのブラウザで自動的に開いてくれる。(至れり尽くせり)

#### 4. ブログ記事を修正

```sh
$ cdk posts
$ vim hello_world.jade
```

修正して保存すると、ブラウザが自動でリロードされ、すぐさま修正結果を確認出来る。
すごい・・・。

---

##### 参考:

[Node.js - 静的サイトジェネレーター「roots」 - Qiita](http://qiita.com/morishitter/items/b708a11e09b6fcb2f751)

[静的サイトジェネレータ「roots」を触ってみた | REFLECTDESIGN](http://re-dzine.net/2013/02/roots/)

