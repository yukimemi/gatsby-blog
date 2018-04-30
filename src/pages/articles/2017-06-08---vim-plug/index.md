---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
- nvim
Description: ""
Tags:
- plugin
- vim
- nvim
- vim-plug
date: "2017-06-08T07:43:37+09:00"
title: Setting of the vim-plug
---

ずっと [dein.vim](https://github.com/Shougo/dein.vim) を使っていたのだけれど、ちょっと他のも試しに使ってみるかと思って、こっちも人気のある、 [vim-plug](https://github.com/junegunn/vim-plug) を試しに使ってみた。

設定は公式のREADMEとかを参考にして、こんな感じになった。
(全部じゃないけど、抜粋して)

```vim
" Plugin: {{{1
" Use vim-plug.
let s:cache_home = expand('~/.cache/nvim')
let s:plug_dir = s:cache_home . '/plugs'
let s:vim_plug_dir = s:cache_home . '/vim-plug'
if has('vim_starting')
  if !isdirectory(s:vim_plug_dir)
    echo "Install vim-plug ..."
    execute '!git clone --depth 1 https://github.com/junegunn/vim-plug.git ' . s:vim_plug_dir . '/autoload'
  endif
  execute 'set runtimepath^=' . fnamemodify(s:vim_plug_dir, ':p')
endif

" Helper function.
function! Cond(cond, ...)
  let opts = get(a:000, 0, {})
  return a:cond ? opts : { 'on': [], 'for': [] }
endfunction

function! MakeVimproc(info) abort "{{{2
  if a:info.status == 'updated' && g:is_windows && !has('kaoriya')
    let g:vimproc#download_windows_dll = 1
  endif
  if !g:is_windows
    !make
  endif
endfunction

" Plugin list. {{{2
call plug#begin(s:plug_dir)

Plug 'joshdick/onedark.vim'
Plug 'itchyny/vim-cursorword'
Plug 'taku-o/vim-zoom', Cond(has('gui'))
Plug 'Yggdroot/indentLine'
Plug 'Shougo/deoplete.nvim', Cond(has('nvim'), { 'do': 'UpdateRemotePlugins' })
Plug 'Shougo/neocomplete.vim', Cond(!has('nvim'))
Plug 'Shougo/context_filetype.vim'
Plug 'Shougo/denite.nvim', { 'on': 'Denite' }
Plug 'airblade/vim-rooter'
Plug 'Shougo/vimproc.vim', Cond(!has('kaoriya'), { 'do': function('MakeVimproc') })
Plug 'glidenote/memolist.vim', { 'on': ['Memolist', 'MemoNew'] }
Plug 'mattn/sonictemplate-vim', { 'on': 'Templete' }
Plug 'junegunn/vim-easy-align', { 'on': '<Plug>(EasyAlign)' }
Plug 'fatih/vim-go', { 'for': 'go' }
Plug 'rust-lang/rust.vim', Cond(executable('cargo'), { 'for': 'rust' })
Plug 'b4b4r07/vim-sqlfmt', { 'for': 'sql', 'do': 'go get github.com/jackc/sqlfmt' }

call plug#end()

" Plugin settings: {{{1
let s:p = { 'plugs': get(g:, 'plugs', {}) }
function! s:p.is_installed(name) abort
  return has_key(self.plugs, a:name) ? isdirectory(self.plugs[a:name].dir) : 0
endfunction

" vim-cursorword {{{2
if s:p.is_installed('vim-cursorword')
  function! s:ToggleCursorWord() abort
    let b:cursorword = !get(b:, 'cursorword', 1)
  endfunction

  com! ToggleCursorWord call s:ToggleCursorWord()
endif

" deoplete.nvim {{{2
if s:p.is_installed('deoplete.nvim')
  let g:deoplete#enable_at_startup = 1
endif

" neocomplete.vim {{{2
if s:p.is_installed('neocomplete.vim')
  let g:neocomplete#enable_at_startup = 1
endif

" denite.nvim {{{2
if s:p.is_installed('denite.nvim')
  " Use plefix s
  nnoremap suc :<C-u>Denite colorscheme -auto-preview<CR>
  nnoremap sub :<C-u>Denite buffer<CR>
  nnoremap suf :<C-u>Denite file<CR>
  nnoremap suF :<C-u>Denite file_rec<CR>
  " nnoremap suu :<C-u>Denite buffer file_old<CR>
  nnoremap suu :<C-u>Denite buffer file_mru<CR>
  nnoremap suo :<C-u>Denite outline -no-quit -mode=normal<CR>
  nnoremap suh :<C-u>Denite help<CR>
  nnoremap sur :<C-u>Denite register<CR>
  nnoremap sug :<C-u>Denite grep -no-empty<CR>
  nnoremap su/ :<C-u>Denite line -no-quit<CR>
  nnoremap suR :<C-u>Denite -resume<CR>

  noremap sul :<C-u>Denite command_history<CR>

  " Incremental search in cmdline history.
  inoremap <C-l> <ESC>:<C-u>Denite command<CR>

  au! User denite.nvim call s:denite_cfg()

  function! s:denite_cfg() abort
    " Load dependent plugins.
    call plug#load('neomru.vim')
    " Default options.
    call denite#custom#option('default', {
          \ 'prompt': '»',
          \ 'cursor_wrap': v:true,
          \ 'auto_resize': v:true,
          \ 'highlight_mode_insert': 'WildMenu'
          \ })
    " Pt command on grep source
    if executable('pt')
      call denite#custom#var('grep', 'command', ['pt'])
      call denite#custom#var('grep', 'default_opts',
            \ ['--nogroup', '--nocolor', '--smart-case'])
      call denite#custom#var('grep', 'recursive_opts', [])
      call denite#custom#var('grep', 'pattern_opt', [])
      call denite#custom#var('grep', 'separator', ['--'])
      call denite#custom#var('grep', 'final_opts', [])
    endif
    " custom mappings.
    call denite#custom#map('insert', '<C-n>', '<denite:move_to_next_line>', 'noremap')
    call denite#custom#map('insert', '<C-p>', '<denite:move_to_previous_line>', 'noremap')
    call denite#custom#map('insert', '<C-[>', '<denite:enter_mode:normal>', 'noremap')
    call denite#custom#map('normal', '<C-[>', '<denite:quit>', 'noremap')
  endfunction
endif
```

[dein.vim](https://github.com/Shougo/dein.vim) のが色々出来るけど、 [vim-plug](https://github.com/junegunn/vim-plug) もけっこう色々出来てびっくり。
何より、インストール画面がかっちょいいのがいいｗ

- - -

##### 参考

[faq · junegunn/vim-plug Wiki](https://github.com/junegunn/vim-plug/wiki/faq)

[おい、NeoBundle もいいけど vim-plug 使えよ - Qiita](http://qiita.com/b4b4r07/items/fa9c8cceb321edea5da0)

