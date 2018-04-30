---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- roots
Description: ""
Tags:
- roots
- jquery
- simplepagination
date: "2014-07-09"
title: roots simple pagination
---

rootsは非常にシンプルで良いのだが、シンプルが故に通常のブログだと当たり前な部分を自分で実装しなければいけないところがある。

その一つが、ページネーション。

rootsでブログテンプレートを作成した場合、トップページはページネーションされず、書いていったブログ記事が全部リストで並ぶことになる。

まぁそんなたいした量を書いてないんで問題ないのだけれども、一応ブログとしてページネーションくらいはあったほうがいいと思い、実装方法を探してみた。

rootsは静的サイトジェネレーターなので、通常だとjadeの記法などでコンパイル時に出来るんだろうけど、今回はjqueryで簡単に出来る [simplePagination.js](http://flaviusmatis.github.io/simplePagination.js/) というものが見つかったので、それを追加してみた。

### インストール
まず、追加するのにはbowerを使った。
ちなみにローカルにインストールしたbowerを使う方法は、[前の記事](http://yukimemi.github.io/posts/2014-07-02_direnv%20for%20npm%20local%20commands.html) を参照。

```sh
$ npm install bower --save-dev
```

事前準備として、bowerのコンポーネントがインストールされる先を変更しておく。

roots のルートで、
```sh
$ vim .bowerrc
```
し、以下を記載。
```javascript
{
    "directory": "assets/bower_components"
}
```

その後、bowerにて、 [simplePagination.js](http://flaviusmatis.github.io/simplePagination.js/) をインストールする。
```sh
$ bower install jquery.simplePagination --save
```

これで、 `assets/bower_components` 配下に保存される。

### [simplePagination.js](http://flaviusmatis.github.io/simplePagination.js/) 設定

#### jade側の設定
まずは、ページネーションの配置。
```sh
$ vim views/layout.jade
```

```javascript
--- 省略 ---
#main
  != content
  .pagination
--- 省略 ---
```
divのclassで設定。単純に `pagination` というclassにて、divを配置しただけ。

それから、stylusでの設定がしやすいように、ブログ記事のリストにclassを設定しておく。

```sh
$ vim views/index.jade
```

```jade
ul#posts
  - each post in sort(site.posts, { by: 'date' })
    li.plist
      p
        a(href="\#{post.url}")= post.title
```

`ul#posts -> li` に、class `plist` を追加しただけ。

#### stylus側の設定
```sh
$ vim assets/css/master.styl
```

最初はすべてのブログ記事リストを見えなくしておく。

```stylus
#posts
  .plist
    display: none
```

#### coffee側の設定
`assets/js/main.coffee` に設定を記述していく。

```sh
$ vim assets/js/main.coffee
```

記載内容 ↓
```javascript
#= require "_helper"

# Add scripts to load to this array. These can be loaded remotely like jquery
# is below, or can use file paths, like 'vendor/underscore'
jq = ["http://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"]
js = ["/bower_components/jquery.simplePagination/jquery.simplePagination.js"]


# this will fire once the required scripts have been loaded
require jq, ->
  require js, ->
    $ ->
      console.log 'jquery loaded, dom ready <-- via assets/js/main.coffee'

      # Pagination
      items = $('#posts li')
      numItems = items.length
      perPage = 21

      # only show the first perPage items initially
      items.slice(0, perPage).show()

      # now setup your pagination
      $('.pagination').pagination
        items: numItems
        itemsOnPage: perPage
        cssStyle: "light-theme"
        onPageClick: (pageNumber) ->
          showFrom = perPage * (pageNumber - 1)
          showTo = showFrom + perPage

          items.hide()
               .slice(showFrom, showTo).show()
```
javascriptのロードはrequire.jsで行うので、jade側への追記はなし。

rootsはデフォルトで、jqueryのロードは行っているようだったので、追加で [simplePagination.js](http://flaviusmatis.github.io/simplePagination.js/) の設定を追加していった。
最初、jqueryと同じ配列に [simplePagination.js](http://flaviusmatis.github.io/simplePagination.js/) を組み込んでいたのだが、 [simplePagination.js](http://flaviusmatis.github.io/simplePagination.js/) はjqueryが読み込まれた後でないと正常に実行されないようなので、必ずjqueryが先に読み込まれるように、jqueryとそれ以外というように配列を分け、コールバックを一つ深くした。

- 16L でまず対象のリストのセレクタをゲット
- 17L で、リストの数をゲット。
- 18L で、1ページに表示するリストの数を指定。
- 21L で、最初の1ページだけを表示。
- 24L から、 [simplePagination.js](http://flaviusmatis.github.io/simplePagination.js/) の設定。
- 25L で、 17L でゲットしたリストの数を指定。
- 26L で、 18L で指定した1ページに表示するリストの数を設定。
- 27L で、 [simplePagination.js](http://flaviusmatis.github.io/simplePagination.js/) のテーマを指定。
- 28L から、ページネーションのボタンが押下された時の実装を指定。現在のページ番号が `pageNumber` に入っている。
- 29L で、表示するリストの開始番号を算出。
- 30L で、表示するリストの終了番号を算出。
- 32-33L で、一度リストを全部非表示にして、必要なところだけ表示する。


今度はjadeでコンパイル時にページネーション実装してみたい。

- - -

##### 参考
[simplePagination.js](http://flaviusmatis.github.io/simplePagination.js/)

[javascript - How to use SimplePagination jquery - Stack Overflow](http://stackoverflow.com/questions/20896076/how-to-use-simplepagination-jquery)

