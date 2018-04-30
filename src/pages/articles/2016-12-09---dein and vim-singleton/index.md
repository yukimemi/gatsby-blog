---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
- neovim
Description: ""
Tags:
- vim
- neovim
- dein
- singleton
date: "2016-12-09T10:20:28+09:00"
title: dein and vim singleton
---

[vim-singleton](http://thinca.hatenablog.com/entry/20120201/1328099090) という便利な vim plugin がある。

vim の clientserver 機能を使って、ファイルをすでに起動している vim で開くことができる。

これを [dein.vim](https://github.com/Shougo/dein.vim) と一緒に使用する時の方法がわからなかったので、 twitter でつぶやいてみた。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr"><a href="https://twitter.com/yukimemi">@yukimemi</a> hook_source を使ってください。hook_add はロード前に呼ばれるのでエラーとなります</p>&mdash; 暗黒美夢王(deoplete dev) (@ShougoMatsu) <a href="https://twitter.com/ShougoMatsu/status/805623471589892096">2016年12月5日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

そしたらなんとあの Shougo さんからお返事が！！

親切に教えてもらえました！

そこで教えてもらった通り、 `hook_add` から `hook_source` に変更。

こんな感じ。

- dein.toml

```toml
[[plugins]]
repo = 'thinca/vim-singleton'
if = "!has('nvim')"
hook_source = '''
  call singleton#enable()
'''
```

それから、 lazy でないプラグインは、 `hook_source` は動かないため、 `dein` のドキュメントに書いてあるように `dein#call_hook('source')` を手動で呼ぶ必要がある。

- init.vim

```vim
set shellslash
if has('nvim')
  let $VIM_PATH = expand('~/.config/nvim')
  let $MYVIMRC = expand('~/.config/nvim/init.vim')
else
  let $VIM_PATH = expand('~/.vim')
  let $MYVIMRC = expand('~/.vimrc')
  let $MYGVIMRC = expand('~/.gvimrc')
endif

" Use dein.
if has('nvim')
  let s:cache_home = expand('~/.cache/nvim')
else
  let s:cache_home = expand('~/.cache/vim')
endif

let s:dein_dir = s:cache_home . '/dein'
let s:dein_repo_dir = s:dein_dir . '/repos/github.com/Shougo/dein.vim'
if !isdirectory(s:dein_repo_dir)
  execute '!git clone https://github.com/Shougo/dein.vim ' . s:dein_repo_dir
endif
execute 'set runtimepath^=' . fnamemodify(s:dein_repo_dir, ':p')

let g:dein#install_max_processes = 16
let g:dein#install_progress_type = 'title'
let g:dein#enable_notification = 1
let s:toml_file = $VIM_PATH . '/dein.toml'
if dein#load_state(s:dein_dir)
  call dein#begin(s:dein_dir, [$MYVIMRC, s:toml_file])
  call dein#load_toml(s:toml_file)
  call dein#end()
  call dein#save_state()
endif
call dein#call_hook('source')

" Check and install.
if has('vim_starting') && dein#check_install()
  call dein#install()
endif

" After dein
filetype plugin indent on
syntax enable
```

自分の設定はこんな感じ。
めちゃべんり。

- - -
##### 参考

[singleton.vim 作った | 永遠に未完成](http://thinca.hatenablog.com/entry/20120201/1328099090)

[dein.vim doc](https://github.com/Shougo/dein.vim/blob/master/doc/dein.txt)


