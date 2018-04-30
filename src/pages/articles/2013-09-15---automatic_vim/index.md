---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
Description: ""
Tags:
- vim
- vim-automatic
date: "2013-09-15"
title: vim-automatic <C-[><C-[> でウィンドウクローズ
---

[automatic.vim](https://github.com/osyo-manga/vim-automatic)
という便利なプラギンがある。

これを利用すると、 Unite や QuickFix や help
など、様々なウィンドウを一元管理出来るっぽい。

そこで、いろんなサイトを参考にやってみた。

```vim
" ESC2回押しで検索ハイライトを消去
autocm MyAutoCmd VimEnter,WinEnter,WinLeave * nmap <silent> <ESC><ESC> :<C-u>nohlsearch<CR><Plug>(anzu-clear-search-status)

" vim-automatic {{{
function! s:my_temporary_window_init(config, context)
    silent! nunmap <ESC><ESC>
    nmap <buffer> <C-[> :<C-u>q<CR>
    nmap <buffer> <ESC> :<C-u>q<CR>
endfunction

let g:automatic_default_match_config = {
            \   'is_open_other_window': 1
            \ }
let g:automatic_default_set_config = {
            \   'height': '60%',
            \   'move': 'bottom',
            \   'apply': function('s:my_temporary_window_init')
            \ }
let g:automatic_config = [
            \   {'match': {'buftype': 'help'}},
            \   {'match': {'bufname': '^.vimshell'}},
            \   {'match': {'bufname': '[\[\*]unite[\]\*]'}},
            \   {
            \       'match': {
            \           'filetype': 'qf',
            \           'autocmds': ['FileType']
            \       },
            \   },
            \   {
            \       'match': {
            \           'filetype': '\v^ref-.+',
            \           'autocmds': ['FileType']
            \       }
            \   },
            \   {
            \       'match': {
            \           'bufname': '\[quickrun output\]'
            \       },
            \       'set': {
            \           'height': 8
            \       }
            \   },
            \   {
            \       'match': {
            \           'autocmds': ['CmdwinEnter']
            \       },
            \       'set': {
            \           'is_close_focus_out': 1,
            \           'unsettings': ['move', 'resize']
            \       }
            \   }
            \ ]
" }}}
```

これで、 Unite 、 QuickFix 、 help で、 `<C-[><C-[>`
を押すと閉じることが出来る。 `<C-[><C-[>`
で検索後のハイライトを消すというマッピングを行っているため、わざわざ一回マッピング削除して
さらにもう一回設定みたいなことをやってるのがなんかカッコ悪い・・・。

もうちょっとうまく出来ると思うんだけど、 vim 力足りず・・・。

##### 参考:

[automatic.vimでウィンドウの一時的分割をハンドリングしよう |かなりすごいブログ](http://blog.supermomonga.com/articles/vim/automatic.html)

[osyo-manga/vim-automatic GitHub](https://github.com/osyo-manga/vim-automatic)
