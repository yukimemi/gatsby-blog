---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
Description: ""
Tags:
- vim
- neovim
- pack
- minpac
date: "2017-09-17T23:30:20+09:00"
title: Use minpac.vim
---

vim や neovim で標準で用意されている `package` 機能。まだ一度も触ったことがなかったので使ってみた。

使ってみるに当たって、 [k-takata/minpac](https://github.com/k-takata/minpac) が
便利そうなので、利用してみた。

```vim
" Plugin:
" Use minpac. {{{1
set packpath^='~/.cache/nvim/'
let s:minpac_dir = '~/.cache/nvim/pack/minpac/opt/minpac'
if has('vim_starting')
  if !isdirectory(s:minpac_dir)
    echo "Install minpac ..."
    execute '!git clone --depth 1 https://github.com/k-takata/minpac ' . s:minpac_dir
  endif
endif

" pack list. {{{1
" start plugins. {{{2
let s:start_plugs = [
      \ ['Yggdroot/indentLine', {}],
      \ ['itchyny/lightline.vim', {}],
      \ ]

" opt plugins. {{{2
let s:opt_plugs = [
      \ ['fatih/vim-go', {'type': 'opt'}],
      \ ['zchee/deoplete-go', {'type': 'opt', 'do': 'silent! !make'}],
      \ ['kristijanhusak/vim-hybrid-material', {'type': 'opt'}],
      \ ]

" lazy load plugins. {{{2
let s:lazy_plugs = [
      \ ['Konfekt/FastFold', {'type': 'opt'}],
      \ ['thinca/vim-submode', {'type': 'opt'}],
      \ ['LeafCage/yankround.vim', {'type': 'opt'}],
      \ ['tyru/capture.vim', {'type': 'opt'}],
      \ ['Shougo/context_filetype.vim', {'type': 'opt'}],
      \ ['Shougo/denite.nvim', {'type': 'opt', 'do': 'silent! UpdateRemotePlugins'}, has('python3')],
      \ ['Shougo/deoplete.nvim', {'type': 'opt', 'do': 'silent! UpdateRemotePlugins'}, has('nvim')],
      \ ['Shougo/echodoc.vim', {'type': 'opt'}],
      \ ['Shougo/vimproc.vim', {'type': 'opt', 'do': 'silent! !make'}],
      \ ]

" minpac init. {{{1
if exists('*minpac#init')
  " minpac is loaded.
  call minpac#init()
  call minpac#add('k-takata/minpac', {'type': 'opt'})

  function! s:minpac_add(plugs)
    for plug in a:plugs
      let cond = len(plug) > 2 ? plug[2] : 1
      if cond
        exe 'call minpac#add("' . plug[0] . '", ' . string(plug[1]) . ')'
      endif
    endfor
  endfunction

  " start. {{{2
  call <SID>minpac_add(s:start_plugs)

  " opt. {{{2
  call <SID>minpac_add(s:opt_plugs)

  " lazy. {{{2
  call <SID>minpac_add(s:lazy_plugs)
endif

" Async load lazy plugins. {{{1
let s:idx = 0
function! PackAddHandler(timer)
  let plug = s:lazy_plugs[s:idx]
  let name = split(plug[0], '/')[1]
  let cond = len(plug) > 2 ? plug[2] : 1
  if cond
    " exe "echom 'packadd " . name . "'"
    exe 'packadd ' . name
  endif
  let s:idx += 1
  " doautocmd BufReadPost
  au! lazy_load_bundle
endfunction

aug lazy_load_bundle
  au MyAutoCmd VimEnter * call timer_start(1, 'PackAddHandler', {'repeat': len(s:lazy_plugs)})
aug END

" Plugin settings. {{{1
" lightline. {{{2
let g:lightline = {
      \ 'colorscheme': 'jellybeans',
      \ }

" deoplete.nvim. {{{2
let g:deoplete#enable_at_startup = 1

" vim-submode. {{{2
let g:submode_leave_with_key = 1

au MyAutoCmd VimEnter * call <SID>vim_submode_aft()
function! s:vim_submode_aft() abort
  packadd vim-submode
  call submode#enter_with('bufmove', 'n', '', 's>', '<C-w>>')
  call submode#enter_with('bufmove', 'n', '', 's<', '<C-w><')
  call submode#enter_with('bufmove', 'n', '', 's+', '<C-w>+')
  call submode#enter_with('bufmove', 'n', '', 's-', '<C-w>-')
  call submode#map('bufmove', 'n', '', '>', '<C-w>>')
  call submode#map('bufmove', 'n', '', '<', '<C-w><')
  call submode#map('bufmove', 'n', '', '+', '<C-w>+')
  call submode#map('bufmove', 'n', '', '-', '<C-w>-')
endfunction

" yankround.vim. {{{2
nmap p <Plug>(yankround-p)
nmap P <Plug>(yankround-P)
nmap <C-p> <Plug>(yankround-prev)
nmap <C-n> <Plug>(yankround-next)
let g:yankround_max_history = 100

" vim-go. {{{2
let g:go_auto_type_info = 1
let g:go_snippet_engine = "neosnippet"
let g:go_fmt_command = "goimports"

let g:go_metalinter_autosave = 1
let g:go_fmt_autosave = 0
let g:go_gocode_unimported_packages = 1
" au MyAutoCmd BufWritePost *.go GoMetaLinter
" au MyAutoCmd BufWritePre *.go silent GoFmt

au MyAutoCmd BufNew,BufRead *.go call <SID>vim_go_cfg()

function! s:vim_go_cfg() abort
  packadd vim-go

  nmap <buffer> <Leader>gd <Plug>(go-doc)
  nmap <buffer> <Leader>gs <Plug>(go-doc-split)
  nmap <buffer> <Leader>gv <Plug>(go-doc-vertical)
  nmap <buffer> <Leader>gb <Plug>(go-doc-browser)
  nmap <buffer> <Leader>gr <Plug>(go-rename)
  nnoremap <buffer> <Leader>gi :<C-u>GoImport<Space>
  setl completeopt=menu,preview
endfunction

" Define user commands for updating/cleaning the plugins. {{{1
" Each of them loads minpac, reloads .vimrc to register the
" information of plugins, then performs the task.
com! PackClean     packadd minpac | source $MYVIMRC | call minpac#clean()
com! PackUpdate    packadd minpac | source $MYVIMRC | call minpac#clean() | call minpac#update()
com! PackListStart packadd minpac | source $MYVIMRC | Capture echo minpac#getpackages("", "start")
com! PackListOpt   packadd minpac | source $MYVIMRC | Capture echo minpac#getpackages("", "opt")
com! PackNameStart packadd minpac | source $MYVIMRC | Capture echo minpac#getpackages("", "start", "", 1)
com! PackNameOpt   packadd minpac | source $MYVIMRC | Capture echo minpac#getpackages("", "opt", "", 1)
```

設定するに当たって、 [Vim の起動速度2 - Memo](http://d.hatena.ne.jp/heavenshell/20160430/1462088882) を参考に (というかほぼパク・・・) した。

`package` には、 `start` と、 `opt` がある。 `start` は、デフォルトで読み込まれるプラグインで、 `opt` なものは、別途 `packadd` をすることで
遅延ロードすることが出来る。

僕は 3つの 種類に分けて設定してみた。

`s:start_plugs` に記載しているプラグインは、文字通り `start` でデフォルトで読み込まれるプラグインたち。

`s:opt_plugs` に記載しているプラグインは、 特定の `filetype` で有効化するようなやつ。これらはその `filetype` の
`autocmd` で個別に `packadd` する。
ちなみに、 `colorscheme` も、 `opt` でいいらしい。 `colorscheme` コマンドが使用される時に自動的に `packadd` されるよう。

んで、 `s:lazy_plugs` に記載しているのが、遅延ロードするやつ。
[Vim の起動速度2 - Memo](http://d.hatena.ne.jp/heavenshell/20160430/1462088882) で書かれているように、
タイマー機能を使用して、 `vim` が起動した後に遅延で `packadd` する。

これで、起動時間を計測したところ・・・ [dein.vim](https://github.com/Shougo/dein.vim) より、少し遅いくらいになった・・・。

[dein.vim](https://github.com/Shougo/dein.vim) めちゃ早いことがわかった・・・。

- - -

##### 参考

[Vim の起動速度2 - Memo](http://d.hatena.ne.jp/heavenshell/20160430/1462088882)

[k-takata/minpac: A minimal package manager for Vim 8 (and Neovim)](https://github.com/k-takata/minpac)

[Vimのパッケージ機能を試してみました - Blank File](http://h-miyako.hatenablog.com/entry/2016/02/29/211534)


