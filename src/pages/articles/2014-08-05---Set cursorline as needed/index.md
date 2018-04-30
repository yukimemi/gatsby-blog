---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
Description: ""
Tags:
- vim
- cursorline
- vimrc
date: "2014-08-05"
title: Set cursorline as needed
---

これまで、 ['cursorline' を必要な時にだけ有効にする - 永遠に未完成](http://d.hatena.ne.jp/thinca/20090530/1243615055) を参考に、cursorlineの設定をしていたけど、時々cursorlineがずっと表示されたままになる現象が発生していた。

よくよく調べてみると、一度 CursorHold イベントを発生させ(set cursorline)、その後に別Windowへ移動し、さらに `<C-^>` でもとのバッファに戻ったときに、 `s:cursorline_lock` 変数が「0」のままとなり、その後 CursorMoved イベントが発生してもずっと `set nocursorline` されなくなるようだった。

そこで、 Lingr のvim部屋で初めて質問してみた。

そしたらあっさりと答えを教えてもらえた。vimmerは思ってたよりこわくなかった。よかた。

`<C-^>` では、 `WinEnter` イベントは発生しないが、 `BufEnter` イベントは発生するらしい。
また、僕は、 `nnoremap : q:i` のようにして、コマンドラインウィンドウを使うようにしているのだが、この場合も上記と同様に、cursorlineがずっと消えない事象が発生していた。
そこで、thincaさんの [設定](http://d.hatena.ne.jp/thinca/20090530/1243615055) を以下のように、変更した。

```vim
au MyAutoCmd CursorMoved,CursorMovedI * call s:auto_cursorline('CursorMoved')
au MyAutoCmd CursorHold,CursorHoldI * call s:auto_cursorline('CursorHold')
au MyAutoCmd WinEnter,BufEnter,CmdwinLeave * call s:auto_cursorline('WinEnter,BufEnter,CmdwinLeave')
au MyAutoCmd WinLeave * call s:auto_cursorline('WinLeave')

let s:cursorline_lock = 0
function! s:auto_cursorline(event)
  if a:event ==# 'WinEnter,BufEnter,CmdwinLeave'
    setlocal cursorline
    setlocal cursorcolumn
    let s:cursorline_lock = 2
  elseif a:event ==# 'WinLeave'
    setlocal nocursorline
    setlocal nocursorcolumn
  elseif a:event ==# 'CursorMoved'
    if s:cursorline_lock
      if 1 < s:cursorline_lock
        let s:cursorline_lock = 1
      else
        setlocal nocursorline
        setlocal nocursorcolumn
        let s:cursorline_lock = 0
      endif
    endif
  elseif a:event ==# 'CursorHold'
    if &updatetime >= 4000
      setlocal cursorline
      setlocal cursorcolumn
    endif
    let s:cursorline_lock = 1
  endif
endfunction
```

ついでに `cursorcolumn` も追加しといた。
また、 [TweetVim](https://github.com/basyura/TweetVim) や、 [calendar.vim](https://github.com/itchyny/calendar.vim) では、 updatetime が短すぎるため、すぐに `CursorHold` イベントが発生してしまう。なので、通常の updatetime (4000) 以上の場合だけ、 `set cursorline` するようにしている。

こんないろんな設定出来るvimすごい。

