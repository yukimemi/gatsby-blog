---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
Description: ""
Tags:
- vim
- flowtype
date: "2016-09-18T13:03:18+09:00"
title: flowtype in vim
---

最近 `flowtype` という `Altjs` があるのを知った。

facebookが作っているらしく、 `typescript` と違って、既存のjavascriptをそのまま使うことも出来、型エラーがあってもコンパイル、実行が出来る。

導入は `npm` で簡単にインストール出来る。

```sh
$ npm i -g flow-bin
```

`flowtype` 自体の説明は
[型なき世界のためのflowtype入門 - Qiita](http://qiita.com/mizchi/items/95ee0101ac22e4b7b662)
とか、公式の
[Flow | A static type checker for JavaScript](https://flowtype.org/)
とか見ればいいと思う。

んで、公式にも書いてあるように、vimやemacsのプラギンが提供されている。

[flowtype/vim-flow: A vim plugin for Flow](https://github.com/flowtype/vim-flow)


`neovim` `dein` でのインストール手順。
`toml` を使っている前提。

```vim
[[plugins]]
repo = 'flowtype/vim-flow'
on_ft = ['javascript', 'javascript.jsx']
build = 'npm install -g flow-bin'
hook_add = '''
  let g:flow#autoclose = 1
'''

[[plugins]]
repo = 'Shougo/deoplete.nvim'
if = "has('nvim')"
on_i = 1
hook_add = '''
  let g:deoplete#enable_at_startup = 1
  let g:deoplete#omni_patterns = {}
  let g:deoplete#omni_patterns.javascript = '[^. *\t]\.\w*'
'''
```

これで、 `deoplete` を使って自動補完が出来、 `flowtype` が記述出来た。

ちなみに、 `javascript` の `syntax` とかは、以下の設定がいいもよう。

```vim
[[plugins]]
repo = 'pangloss/vim-javascript'
on_ft = ['javascript', 'javascript.jsx']

[[plugins]]
repo = 'mxw/vim-jsx'
on_ft = ['javascript', 'javascript.jsx']

[[plugins]]
repo = 'othree/yajs.vim'
on_ft = ['javascript', 'javascript.jsx']

[[plugins]]
repo = 'maxmellon/vim-jsx-pretty'
on_ft = ['javascript', 'javascript.jsx']

[[plugins]]
repo = 'othree/javascript-libraries-syntax.vim'
on_ft = ['javascript', 'javascript.jsx']

[[plugins]]
repo = 'othree/es.next.syntax.vim'
on_ft = ['javascript', 'javascript.jsx']
```

- - -
##### 参考

[flowtype/vim-flow: A vim plugin for Flow](https://github.com/flowtype/vim-flow)

[vimのECMASCript2015環境をイイ感じにする - Qiita](http://qiita.com/maxmellon/items/0e6173cdd51843053839)

