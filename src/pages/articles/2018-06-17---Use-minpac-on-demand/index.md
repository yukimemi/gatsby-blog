---
title: Use minpac and source plugin on demand
date: "2018-06-18T01:13:17+09:00"
layout: post
draft: false
category: "vim"
tags:
  - "vim"
  - "minpac"
description:
  vim でパッケージマネージャといえば、 Shougo/dein.vim が有名だけど、それ以外でも、 k-takata/minpac もあまり知られてないかもしれないけどシンプルで使いやすい。
---

vim でパッケージマネージャといえば、 [Shougo/dein.vim](https://github.com/Shougo/dein.vim) が有名だけど、それ以外でも、 [k-takata/minpac](https://github.com/k-takata/minpac) もあまり知られてないかもしれないけどシンプルで使いやすい。

前回の [記事](/Use%20minpac.vim/) では、 vim 起動後にプラグインを遅延で読み込む、というのをやったけど、それだけじゃなく、 dein みたいに、特定の Command 実行時、もしくは特定の FileType で必要に応じてロードする、っていうのをやってみようと思う。

vim では、前者の特定の Command 時にそのコマンドが定義されていなかったら・・・という autocmd のイベントが定義されている。( CmdUndefined )
また、後者の特定の FileType も同様に autocmd のイベントが定義されている。 ( FileType )

これを組み込んだ独自のコマンドを定義することで、 dein に似た設定とできた。

```vim
" Use minpac. {{{1
set packpath^=$CACHE_HOME
let s:minpac_dir = $CACHE_HOME . '/pack/minpac/opt/minpac'
if has('vim_starting')
  if !isdirectory(s:minpac_dir)
    echo "Install minpac ..."
    execute '!git clone --depth 1 https://github.com/k-takata/minpac ' . s:minpac_dir
  endif
endif

" minpac init. {{{1
if exists('*minpac#init')
  " minpac is loaded.
  call minpac#init()
  call minpac#add('k-takata/minpac', {'type': 'opt'})
endif

" minpac helper function. {{{1
let s:lazy_plugs = []
function! s:minpac_add(repo, ...) abort
  let l:opts = get(a:000, 0, {})
  if has_key(l:opts, 'if')
    if ! l:opts.if
      return
    endif
  endif

  let l:name = substitute(a:repo, '^.*/', '', '')
  " packadd on filetype.
  if has_key(l:opts, 'ft')
    let l:ft = type(l:opts.ft) == type([]) ? join(l:opts.ft, ',') : l:opts.ft
    exe printf('au MyAutoCmd FileType %s packadd %s', l:ft, l:name)
  endif

  " packadd on cmd.
  if has_key(l:opts, 'cmd')
    let l:cmd = type(l:opts.cmd) == type([]) ? join(l:opts.cmd, ',') : l:opts.cmd
    exe printf('au MyAutoCmd CmdUndefined %s packadd %s', l:cmd, l:name)
  endif

  if has_key(l:opts, 'lazy')
    if l:opts.lazy
      call add(s:lazy_plugs, l:name)
    endif
  endif

  if exists('*minpac#init')
    call minpac#add(a:repo, l:opts)
  endif
endfunction

com! -nargs=+ Pac call <SID>minpac_add(<args>)

" Load lazy plugins. {{{1
let s:idx = 0
function! PackAddHandler(timer)
  exe 'packadd ' . s:lazy_plugs[s:idx]
  let s:idx += 1
  " doautocmd BufReadPost
  au! lazy_load_bundle
  if s:idx == len(s:lazy_plugs)
    echom "lazy load done !"
  endif
endfunction

aug lazy_load_bundle
  au MyAutoCmd VimEnter * call timer_start(0, 'PackAddHandler', {'repeat': len(s:lazy_plugs)})
aug END

" Plugin list. {{{1
" start. {{{2
Pac 'Yggdroot/indentLine'
Pac 'haya14busa/vim-edgemotion'
Pac 'hotwatermorning/auto-git-diff'
Pac 'itchyny/lightline.vim'
Pac 'itchyny/vim-cursorword'
Pac 'itchyny/vim-gitbranch'
Pac 'itchyny/vim-parenmatch'
Pac 'kana/vim-operator-user'
Pac 'kana/vim-textobj-user'
Pac 'kopischke/vim-stay'
Pac 'lambdalisue/vim-findent'
Pac 'prabirshrestha/async.vim'
Pac 'prabirshrestha/asyncomplete-lsp.vim'
Pac 'prabirshrestha/asyncomplete.vim'
Pac 'prabirshrestha/vim-lsp'
Pac 'rhysd/committia.vim'
Pac 'ryanoasis/vim-devicons'
Pac 'ctrlpvim/ctrlp.vim'
Pac 'Shougo/denite.nvim', {'do': 'silent! UpdateRemotePlugins', 'if': has('python3')}

" opt. {{{2
Pac 'NLKNguyen/papercolor-theme', {'type': 'opt'}
Pac 'PProvost/vim-ps1', {'type': 'opt', 'ft': 'ps1'}
Pac 'Shougo/junkfile.vim', {'type': 'opt', 'cmd': 'JunkfileOpen'}
Pac 'airblade/vim-rooter', {'type': 'opt', 'cmd': 'Rooter'}
Pac 'aklt/plantuml-syntax', {'type': 'opt', 'ft': 'plantuml'}
Pac 'alx741/vim-hindent', {'type': 'opt', 'do': 'silent! !stack install hindent', 'if': executable('stack'), 'ft': 'haskell'}
Pac 'b4b4r07/vim-sqlfmt', {'type': 'opt', 'do': 'silent! !go get github.com/jackc/sqlfmt', 'ft': 'sql'}
Pac 'cespare/vim-toml', {'type': 'opt', 'ft': 'toml'}
Pac 'cocopon/iceberg.vim', {'type': 'opt'}
Pac 'cohama/agit.vim', {'type': 'opt', 'cmd': 'Agit*'}
Pac 'dag/vim-fish', {'type': 'opt', 'ft': 'fish'}
Pac 'dhruvasagar/vim-table-mode', {'type': 'opt', 'ft': 'markdown'}
Pac 'eagletmt/ghcmod-vim', {'type': 'opt', 'do': 'silent! !stack install ghc-mod', 'if': executable('stack'), 'ft': 'haskell'}
Pac 'eagletmt/neco-ghc', {'type': 'opt', 'ft': 'haskell'}
Pac 'ekalinin/Dockerfile.vim', {'type': 'opt', 'ft': 'dockerfile'}
Pac 'fatih/vim-go', {'type': 'opt', 'ft': 'go'}
Pac 'glidenote/memolist.vim', {'type': 'opt', 'cmd': 'Memo*'}
Pac 'itchyny/vim-haskell-indent', {'type': 'opt', 'ft': 'haskell'}
Pac 'itchyny/vim-haskell-sort-import', {'type': 'opt', 'ft': 'haskell'}
Pac 'joshdick/onedark.vim', {'type': 'opt'}
Pac 'justinmk/vim-dirvish', {'type': 'opt', 'cmd': 'Dirvish*'}
Pac 'kannokanno/previm', {'type': 'opt', 'ft': 'markdown'}
Pac 'kchmck/vim-coffee-script', {'type': 'opt', 'ft': 'coffee'}
Pac 'keremc/asyncomplete-racer.vim', {'type': 'opt', 'ft': 'rust', 'if': !executable('rls')}
Pac 'kristijanhusak/vim-hybrid-material', {'type': 'opt'}
Pac 'kylef/apiblueprint.vim', {'type': 'opt', 'ft': 'apiblueprint'}
Pac 'leafgarland/typescript-vim', {'type': 'opt', 'ft': ['typescript', 'typescript.tsx']}
Pac 'lifepillar/vim-solarized8', {'type': 'opt'}
Pac 'majutsushi/tagbar', {'type': 'opt', 'cmd': 'Tagbar*'}
Pac 'mattn/sonictemplate-vim', {'type': 'opt', 'cmd': 'Template*'}
Pac 'morhetz/gruvbox', {'type': 'opt'}
Pac 'nelstrom/vim-markdown-folding', {'type': 'opt', 'ft': 'markdown'}
Pac 'neovimhaskell/haskell-vim', {'type': 'opt', 'ft': ['haskell', 'cabal']}
Pac 'othree/es.next.syntax.vim', {'type': 'opt', 'ft': ['javascript', 'javascript.jsx']}
Pac 'othree/javascript-libraries-syntax.vim', {'type': 'opt', 'ft': ['javascript', 'javascript.jsx']}
Pac 'pangloss/vim-javascript', {'type': 'opt', 'ft': ['javascript', 'javascript.jsx']}
Pac 'posva/vim-vue', {'type': 'opt', 'ft': 'vue'}
Pac 'prabirshrestha/asyncomplete-necosyntax.vim', {'type': 'opt', 'ft': 'vim'}
Pac 'prabirshrestha/asyncomplete-necovim.vim', {'type': 'opt', 'ft': 'vim'}
Pac 'qpkorr/vim-renamer', {'type': 'opt', 'cmd': 'Renamer*'}
Pac 'rhysd/rust-doc.vim', {'type': 'opt', 'if': executable('cargo'), 'ft': 'rust'}
Pac 'rhysd/vim-gfm-syntax', {'type': 'opt', 'ft': 'markdown'}
Pac 'rust-lang/rust.vim', {'type': 'opt', 'ft': 'rust'}
Pac 'scrooloose/vim-slumlord', {'type': 'opt', 'ft': 'plantuml'}
Pac 'simnalamburt/vim-mundo', {'type': 'opt', 'cmd': 'MundoToggle*'}
Pac 'stephpy/vim-yaml', {'type': 'opt', 'ft': ['yml', 'yaml']}
Pac 'thinca/vim-qfreplace', {'type': 'opt', 'ft': ['quickfix', 'qf']}
Pac 'tyru/capture.vim', {'type': 'opt', 'cmd': 'Capture'}
Pac 'y0za/vim-reading-vimrc', {'type': 'opt', 'cmd': 'ReadingVimrc*'}

" lazy. {{{2
Pac 'kaneshin/ctrlp-filetype', {'type': 'opt', 'lazy': 1}
Pac 'kaneshin/ctrlp-memolist', {'type': 'opt', 'lazy': 1}
Pac 'kaneshin/ctrlp-sonictemplate', {'type': 'opt', 'lazy': 1}
Pac 'ompugao/ctrlp-history', {'type': 'opt', 'lazy': 1}
Pac 'mattn/ctrlp-launcher', {'type': 'opt', 'lazy': 1}
Pac 'mattn/ctrlp-mark', {'type': 'opt', 'lazy': 1}
Pac 'Shougo/neomru.vim', {'type': 'opt', 'lazy': 1}
Pac 'iyuuya/denite-ale', {'type': 'opt', 'lazy': 1}
Pac 'Konfekt/FastFold', {'type': 'opt', 'lazy': 1}
Pac 'LeafCage/yankround.vim', {'type': 'opt', 'lazy': 1}
Pac 'Shougo/context_filetype.vim', {'type': 'opt', 'lazy': 1}
Pac 'Shougo/echodoc.vim', {'type': 'opt', 'lazy': 1}
Pac 'Shougo/neosnippet-snippets', {'type': 'opt', 'lazy': 1}
Pac 'Shougo/neosnippet.vim', {'type': 'opt', 'lazy': 1}
Pac 'Shougo/vimproc.vim', {'type': 'opt', 'lazy': 1, 'do': 'silent! !make'}
Pac 'gilligan/textobj-lastpaste', {'type': 'opt', 'lazy': 1}
Pac 'haya14busa/incsearch.vim', {'type': 'opt', 'lazy': 1}
Pac 'haya14busa/vim-asterisk', {'type': 'opt', 'lazy': 1}
Pac 'haya14busa/vim-operator-flashy', {'type': 'opt', 'lazy': 1}
Pac 'itchyny/vim-external', {'type': 'opt', 'lazy': 1}
Pac 'itchyny/vim-highlighturl', {'type': 'opt', 'lazy': 1}
Pac 'junegunn/vim-easy-align', {'type': 'opt', 'lazy': 1}
Pac 'kana/vim-operator-replace', {'type': 'opt', 'lazy': 1}
Pac 'kana/vim-textobj-entire', {'type': 'opt', 'lazy': 1}
Pac 'kana/vim-textobj-fold', {'type': 'opt', 'lazy': 1}
Pac 'kana/vim-textobj-function', {'type': 'opt', 'lazy': 1}
Pac 'kana/vim-textobj-indent', {'type': 'opt', 'lazy': 1}
Pac 'kassio/neoterm', {'type': 'opt', 'lazy': 1, 'if': has('nvim')}
Pac 'lambdalisue/gina.vim', {'type': 'opt', 'lazy': 1}
Pac 'ludovicchabant/vim-gutentags', {'type': 'opt', 'lazy': 1, 'if': executable('ctags')}
Pac 'mattn/webapi-vim', {'type': 'opt', 'lazy': 1}
Pac 'ntpeters/vim-better-whitespace', {'type': 'opt', 'lazy': 1}
Pac 'osyo-manga/vim-anzu', {'type': 'opt', 'lazy': 1}
Pac 'osyo-manga/vim-operator-blockwise', {'type': 'opt', 'lazy': 1}
Pac 'osyo-manga/vim-operator-search', {'type': 'opt', 'lazy': 1}
Pac 'osyo-manga/vim-precious', {'type': 'opt', 'lazy': 1}
Pac 'osyo-manga/vim-textobj-multiblock', {'type': 'opt', 'lazy': 1}
Pac 'prabirshrestha/asyncomplete-buffer.vim', {'type': 'opt', 'lazy': 1}
Pac 'prabirshrestha/asyncomplete-emoji.vim', {'type': 'opt', 'lazy': 1}
Pac 'prabirshrestha/asyncomplete-file.vim', {'type': 'opt', 'lazy': 1}
Pac 'prabirshrestha/asyncomplete-neosnippet.vim', {'type': 'opt', 'lazy': 1}
Pac 'prabirshrestha/asyncomplete-tags.vim', {'type': 'opt', 'lazy': 1}
Pac 'rhysd/vim-operator-surround', {'type': 'opt', 'lazy': 1}
Pac 't9md/vim-quickhl', {'type': 'opt', 'lazy': 1}
Pac 'taku-o/vim-zoom', {'type': 'opt', 'lazy': 1}
Pac 'thinca/vim-submode', {'type': 'opt', 'lazy': 1}
Pac 'tpope/vim-repeat', {'type': 'opt', 'lazy': 1}
Pac 'tyru/caw.vim', {'type': 'opt', 'lazy': 1}
Pac 'tyru/open-browser.vim', {'type': 'opt', 'lazy': 1}
Pac 'vim-scripts/autodate.vim', {'type': 'opt', 'lazy': 1}
Pac 'vim-scripts/matchit.zip', {'type': 'opt', 'lazy': 1}
Pac 'w0rp/ale', {'type': 'opt', 'lazy': 1}
Pac 'yami-beta/asyncomplete-omni.vim', {'type': 'opt', 'lazy': 1}
```

・・・にしてもいれてるプラギン多いな・・・。

`Pac` コマンドを定義して、そのコマンドの引数に渡す辞書で動作を分ける感じ。

| key    | value                                                                                                                      |
| :----- | :------------------------------------------------------------------------------------------------------------------------- |
| `type` | minpac デフォルトの設定。 `start` と `opt` が指定できる。                                                                  |
| `do`   | minpac デフォルトの設定。プラグインインストール後、アップデート後に実行されるものを指定できる。                            |
| `if`   | 今回追加したもの。 dein でいう if と同じように、評価後に true となる場合だけプラグインをインストールする。                 |
| `ft`   | 今回追加したもの。 dein でいう on_ft と同じように、指定した FileType のときだけプラグインを有効化 (packadd) する。         |
| `cmd`  | 今回追加したもの。 dein でいう on_cmd と同じように、指定した Command を実行したときだけプラグインを有効化 (packadd) する。 |
| `lazy` | 今回追加したもの。 `1` の場合、 VimEnter イベント後に非同期で裏でプラグインを有効化 (packadd) する。                       |

極力、 `start` に設定するプラグインを少なくして、 vim の起動を早くしようとする作戦。
`start` のものは、起動時からロードされてる。
`opt` のものは、 Command や FileType でロードされる。
`lazy` のものは、 vim 起動後に非同期で裏でロードされる。だけど、結局 dein のがキャッシュとかされるからスピードは早いかもしれない。でも自分の環境ではなぜか dein で [prabirshrestha/asyncomplete.vim](https://github.com/prabirshrestha/asyncomplete.vim) を有効化できなかった。(多分設定が悪いだけなんだろうけど・・・)

とりあえず、 minpac いい感じ。

今回の設定は以下にあるので、もし参考にしたい方はぜひ・・・。

[dotfiles/minpac.vim at master · yukimemi/dotfiles](https://github.com/yukimemi/dotfiles/blob/master/.config/nvim/minpac.vim)

---

#### 参考

[Vim 8.0 Advent Calendar 20 日目 新しいイベント - Qiita](https://qiita.com/thinca/items/dac497efa7d1a2ffd5be)

[Autoload an opt plugin on Filetype. · Issue #28 · k-takata/minpac](https://github.com/k-takata/minpac/issues/28)
