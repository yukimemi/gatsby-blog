---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
Description: ""
Tags:
- vim
- qfixhowm
date: "2013-06-29"
title: qfixhowm Vim でメモ
---

vimでメモをとる場合、簡単な設定では vimrc に JunkFile
コマンドを定義するなどする。

```vim
" JunkFile {{{
" http://vim-users.jp/2010/11/hack181/
  "" Open junk file.
  command! -nargs=0 JunkFile call s:open_junk_file()
  function! s:open_junk_file()
    let l:junk_dir = $HOME . '/.vim_junk'. strftime('/%Y/%m')
    if !isdirectory(l:junk_dir)
      call mkdir(l:junk_dir, 'p')
    endif

    let l:filename = input('Junk Code: ', l:junk_dir.strftime('/%Y-%m-%d-%H%M%S.'))
    if l:filename != ''
      execute 'edit ' . l:filename
    endif
  endfunction
  " }}}
```

これは実行時に拡張子が自由に設定出来るので、簡単にスクリプトを試してみたい場合
はとても便利。

でも、やっぱり最近、メモは統合的に管理したいと思い、 QfixHowm
を導入することにした。

### インストール

Neobundle で一発

```vim
NeoBundle 'fuenor/qfixgrep'
NeoBundle 'fuenor/qfixhowm'
```

qfixgrep もいれとくと何かといいみたい。

### 設定

デフォルトだと拡張子 txt で作成されるため、 markdown
で記録するように 変更する。

```vim
"" qfixhowm {{{
" ファイル拡張子をmdにする
let howm_filename = '%Y/%m/%Y-%m-%d-%H%M%S.md'
" ファイルタイプをmarkdownにする
let QFixHowm_FileType = 'markdown'
" タイトル記号
let QFixHowm_Title = '#'
" タイトル行検索正規表現の辞書を初期化
let QFixMRU_Title = {}
" MRUでタイトル行とみなす正規表現(Vimの正規表現で指定)
let QFixMRU_Title['mkd'] = '^###[^#]'
" grepでタイトル行とみなす正規表現(使用するgrepによっては変更する必要があります)
let QFixMRU_Title['mkd_regxp'] = '^###[^#]'
" }}}
```

こんなんでいいっぽい。

使い方、細かい設定は作者さんのページ参照

##### 参考:

[QFixHowm - vim用howmプラグイン - fudist](https://sites.google.com/site/fudist/Home/qfixhowm)

[Vim-users.jp - Hack \#181: Vimで使い捨てのファイルを作成する](http://vim-users.jp/2010/11/hack181/)

