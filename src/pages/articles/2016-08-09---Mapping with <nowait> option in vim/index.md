---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
Description: ""
Tags:
- vim
- map
date: "2016-08-09T09:58:11+09:00"
title: Mapping with <nowait> option in vim
---

`vim` でマッピングをする時に、便利なオプションがあることを最近知った。

それが `<nowait>` オプション。

例えば、よくやるキーマップの例としてこんなのがある。

```vim
nnoremap <silent> <ESC><ESC> :<C-u>nohlsearch<CR>
```

エスケープ2回でハイライト消すっていうマッピング。

だけど、これを設定した状態で、コマンドウィンドウを `<ESC>` で閉じたい場合、`<ESC>` を押下しても、次のマッピングを待っている状態になってしまって、すぐに閉じることができない。

こんなマッピングをした場合。

```vim
au MyAutoCmd CmdwinEnter * nnoremap <silent><buffer> <ESC> :q<CR>
```

この場合に、 `<nowait>` というオプションを追加することで、 `<ESC>` を押下したらすぐにコマンドウィンドウを閉じることができる。

```vim
au MyAutoCmd CmdwinEnter * nnoremap <silent><buffer><nowait> <ESC> :q<CR>
```

めちゃめちゃ便利。

僕の場合、コマンドウィンドウと、Uniteを閉じるのにこのオプションを使っている。

こんな感じ。

```vim
" Cmdwin.
nnoremap : q:i
vnoremap : q:A

" nohlsearch.
nnoremap <silent> <ESC><ESC> :<C-u>nohlsearch<CR>

" Escape cmd win.
au MyAutoCmd CmdwinEnter * nnoremap <silent><buffer><nowait> <ESC> :q<CR>

" Escape unite.
au MyAutoCmd FileType unite nmap <buffer><nowait> <Esc> <Plug>(unite_exit)
```


