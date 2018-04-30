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
- denite
date: "2017-05-24T18:15:16+09:00"
title: Use denite instead of unite
---

やろうやろうと思っていた、 [Denite](https://github.com/Shougo/denite.nvim) をついに使ってみた。

[dein](https://github.com/Shougo/dein.vim) を使って toml で設定。


```toml
# ==================== Denite ==================== {{{1
[[plugins]]
repo = 'Shougo/denite.nvim'
on_cmd = 'Denite'
if = "has('python3')"
hook_add = '''
  " Use plefix s
  nnoremap suc :<C-u>Denite colorscheme -auto-preview<CR>
  nnoremap sub :<C-u>Denite buffer<CR>
  nnoremap suf :<C-u>Denite file<CR>
  nnoremap suF :<C-u>Denite file_rec<CR>
  nnoremap suu :<C-u>Denite buffer file_old<CR>
  nnoremap suo :<C-u>Denite outline -no-quit -mode=normal<CR>
  nnoremap suh :<C-u>Denite help<CR>
  nnoremap sur :<C-u>Denite register<CR>
  nnoremap sug :<C-u>Denite grep -no-empty<CR>
  nnoremap su/ :<C-u>Denite line<CR>
  nnoremap suR :<C-u>Denite -resume<CR>

  noremap sul :<C-u>Denite command_history<CR>

'''
hook_post_source = '''
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
'''
```

とりあえず unite の時に使ってたのと似た感じで設定してみた。

デフォルトオプションの設定と、キーマッピングの設定を追加でしている。
あと、 grep に pt を使って。

[jvgrep](https://github.com/mattn/jvgrep) を使う設定にもしてみたんだけど、設定方法が悪いのかうまく出来なかったので、放置。 とりあえず pt でいい感じに使える。

- - -

##### 2017/06/10 追記
[Denite](https://github.com/Shougo/denite.nvim) の help を見ると、 [jvgrep](https://github.com/mattn/jvgrep) を使う例が載ってた！！
さすが Shougo さん、ありがとうございます！！

ということで、こんな設定になりました。

```toml
# ==================== Denite ==================== {{{1
[[plugins]]
repo = 'Shougo/denite.nvim'
on_cmd = 'Denite'
if = "has('python3')"
hook_add = '''
  " Use plefix s
  nnoremap suc :<C-u>Denite colorscheme -auto-preview<CR>
  nnoremap sub :<C-u>Denite buffer<CR>
  nnoremap suf :<C-u>Denite file<CR>
  nnoremap suF :<C-u>Denite file_rec<CR>
  nnoremap suu :<C-u>Denite buffer file_old<CR>
  nnoremap suo :<C-u>Denite outline -no-quit -mode=normal<CR>
  nnoremap suh :<C-u>Denite help<CR>
  nnoremap sur :<C-u>Denite register<CR>
  nnoremap sug :<C-u>Denite grep -no-empty<CR>
  nnoremap su/ :<C-u>Denite line<CR>
  nnoremap suR :<C-u>Denite -resume<CR>

  noremap sul :<C-u>Denite command_history<CR>

'''
hook_post_source = '''
  " Default options.
  call denite#custom#option('default', {
        \ 'prompt': '»',
        \ 'cursor_wrap': v:true,
        \ 'auto_resize': v:true,
        \ 'highlight_mode_insert': 'WildMenu'
        \ })
  if executable('jvgrep')
    " jvgrep command on grep source
    call denite#custom#var('grep', 'command', ['jvgrep'])
    call denite#custom#var('grep', 'default_opts', [])
    call denite#custom#var('grep', 'recursive_opts', ['-R'])
    call denite#custom#var('grep', 'pattern_opt', [])
    call denite#custom#var('grep', 'separator', [])
    call denite#custom#var('grep', 'final_opts', [])

  elseif executable('rg')
    " Ripgrep command on grep source
    call denite#custom#var('grep', 'command', ['rg'])
    call denite#custom#var('grep', 'default_opts',
      \ ['--vimgrep', '--no-heading'])
    call denite#custom#var('grep', 'recursive_opts', [])
    call denite#custom#var('grep', 'pattern_opt', ['--regexp'])
    call denite#custom#var('grep', 'separator', ['--'])
    call denite#custom#var('grep', 'final_opts', [])

  elseif executable('pt')
    " Pt command on grep source
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
'''
```

さりげなく、 [ripgrep](https://github.com/BurntSushi/ripgrep) も入れといた。

