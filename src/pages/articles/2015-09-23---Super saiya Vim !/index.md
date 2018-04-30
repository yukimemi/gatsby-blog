---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
Description: ""
Tags:
- vim
- neobundle
- vimrc
date: "2015-09-23T14:54:51+09:00"
title: Super saiya Vim !
---

Vimでも超サイヤ人になりたい！

ってなわけでやってみた。

経緯としては、Windows環境でvimの起動がかなり遅かったため。

vimに導入したプラグイン数が多くなりすぎていたため、起動に時間がかかっていた。
そこで、起動時には基本的なプラグインのみ読み込み、後から必要に応じてpluginを追加で読み込むようにしてみた。


```vim
if has('vim_starting')
  if &compatible
    set nocompatible
  endif
  set runtimepath+=~/.cache/neobundle/neobundle.vim
endif

call neobundle#begin(expand('~/.cache/neobundle'))

if neobundle#load_cache()

  call neobundle#load_toml('~/.vim/vim.d/neobundle_base.toml')
  call neobundle#load_toml('~/.vim/vim.d/neobundlelazy_base.toml', {'lazy' : 1})

  NeoBundleSaveCache
endif

call neobundle#end()

filetype plugin indent on

nnoremap <silent> <Space>s :<C-u>call <SID>loadAllPlugins()<CR>
function! s:loadAllPlugins()
  call neobundle#append()

  call neobundle#load_toml('~/.vim/vim.d/neobundle.toml')
  call neobundle#load_toml('~/.vim/vim.d/neobundlelazy.toml', {'lazy' : 1})

  call neobundle#end()
  filetype plugin indent on
  source ~/.vim/vim.d/05_pluginsetting.vim
  echom "Suuuuuuuuuper !!!!!!!"
endfunction
```

これで、起動時は最小限のpluginで爆速起動し、必要な時に `<Space>s` を押下することで超サイヤVimになれる。(全pluginをロードする)

遅かったWindows環境ではまだ試せていないが、New MacBook 12では、0.2sくらいの起動時間が0.15sにまで短縮された・・・！

#### 参考

[NeoBundleのプラグイン管理をTOMLに任せてvimrcをスッキリさせる | blog: takahiro okumura](http://blog.hifumi.info/2015/03/29/neobundle-load-toml/)

[neobundle の toml 記法サンプル](https://gist.github.com/Shougo/3d2adcb83e9eb0e8d4af)

