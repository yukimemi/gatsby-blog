---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- coffeescript
Description: ""
Tags:
- coffeescript
- vim
- syntax
date: "2013-08-11"
title: Vim coffeescript syntastic
---

vim で保存時に自動で構文チェックとかを行なってくれるプラギンに
Syntastic というものがある。

coffeescript を書く時も、これを使うと便利。

インストールは NeoBundle で一発。

```vim
NeoBundle 'scrooloose/syntastic.git', {
            \ 'build': {
            \   'mac': ['pip install pyflake', 'npm -g install coffeelint'],
            \   'unix': ['pip install pyflake', 'npm -g install coffeelint']
            \ }}
```

上記のように書いておけば、 Syntastic をインストールする時に自動で、
pyflake と coffeelint をインストールしてくれる。 NeoBundle
すごい。

後は普通にコードを書いて、保存すると、自動でエラー行にチェックがつく。
エラーの内容を見たい時は、

```vim
:Errors
```

コマンドでおｋ

coffeelint はデフォルトでインデントは 2 みたい。

.vimrc に以下のように書いておいた。

```vim
autocmd MyAutoCmd BufNewFile,BufRead .coffee setl ft=coffee fenc=utf8 ff=unix tabstop=4 shiftwidth=2 softtabstop=2 expandtab
```

また、1行の長さが 80文字
を超えてもエラーになるのが邪魔だったので、これは無視したい。

以下のファイルの内容を変更すれば、無視することが出来た。(もっとスマートなやり方があるかもしれない。誰か教えてください。)

```diff
$ vim ~/.nodebrew/current/lib/node_modules/coffeelint/lib/coffeelint.js
 max_line_length: {
   value: 80,
   -level: ERROR,
   +level: IGNORE,
   message: 'Line exceeds maximum allowed length'
 },
```
