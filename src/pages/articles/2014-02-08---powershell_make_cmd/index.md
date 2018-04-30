---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
Description: ""
Tags:
- vim
- vim-plugin
- powershell
date: "2014-02-08"
title: vim で powershell を書く！
---

この記事は、 [Vim Advent Calendar 2013](http://atnd.org/events/45072)
70日目の記事になります。

vim で powershell を書く時の設定です。

まず、 syntax と indent 。
これは、以下のプラグインでOK。(なんかインデントは変だけど・・・)

```vim
NeoBundleLazy 'PProvost/vim-ps1'
```

それから、 vim から実行するために、 quickrun
を入れます。(これは定番ですね！)

```vim
NeoBundleLazy 'thinca/vim-quickrun'
```

ちょっと前までは、powershell
でquickrunするためには、設定が必要だったけど、最近オフィシャルに対応されるようになったみたいです。

参考: [VimとPowerShell - Qiita](http://qiita.com/rbtnn/items/ea441a77181d29188880)

これで、powershellを書いてる時に、デフォルトだと \<Leader\>r
で実行できます。

さらに、おもむろに以下のファイルを作成して、 header.cmd
というファイル名で保存。

```bat
@echo off
pushd "%~dp0" > nul
set tm=%time: =0%
set ps1file=%~n0___%date:~-10,4%%date:~-5,2%%date:~-2,2%_%tm:~0,2%%tm:~3,2%%tm:~6,2%%tm:~9,2%.ps1
for /f "usebackq skip=10 delims=" %%i in ("%~f0") do @echo %%i >> "%ps1file%"
powershell -NoProfile -ExecutionPolicy unrestricted -File "%ps1file%" %*
del "%ps1file%"
popd > nul
pause
exit /b %ERRORLEVEL%
# ========== do ps1 file as a dosbatch ==========
```

そして、以下の設定を .vimrc
に書いておくと、powershellスクリプトを手軽に実行できるバッチファイルが作成できます。

```vim
let s:is_windows = has('win16') || has('win32') || has('win64')
let s:system = exists('g:loaded_vimproc') ? 'vimproc#system_bg' : 'system'

if neobundle#tap('vim-ps1')"{{{
    call neobundle#config({
                \ 'autoload': {
                \   'filetypes': 'ps1'
                \ }
                \ })

    function! neobundle#tapped.hooks.on_source(bundle)
        function! s:make_ps12cmd()
            if s:is_windows
                let s:com = "copy /b header.cmd + " . expand("%:p:t") . " " . expand("%:p:t:r") . ".cmd"
            else
                let s:com = "cat header.cmd " . expand("%:p:t") . " > " . expand("%:p:t:r") . ".cmd"
            endif
            echom(s:com)
            call {s:system}(s:com)
        endfunction
        au BufWritePost *.ps1 call s:make_ps12cmd()
    endfunction

    call neobundle#untap()
endif"}}}
```

やってることは単純で、さっきの header.cmd と作成中の powershell
スクリプトを合体させてるだけです。 windows の場合は、 copy
コマンドで。LinuxやMac の場合は、 cat コマンドで。

上記の設定では、保存時に自動で結合されて、元のpowershellファイルの拡張子が"cmd"になったものができあがります。

例えば、以下の hello.ps1 をvim上で保存すると・・・

```ps1
Write-Host "Hello ps1"
```

こんな hello.cmd ができてるはずです。

```bat
@echo off
pushd "%~dp0" > nul
set tm=%time: =0%
set ps1file=%~n0___%date:~-10,4%%date:~-5,2%%date:~-2,2%_%tm:~0,2%%tm:~3,2%%tm:~6,2%%tm:~9,2%.ps1
for /f "usebackq skip=10 delims=" %%i in ("%~f0") do @echo %%i >> "%ps1file%"
powershell -NoProfile -ExecutionPolicy unrestricted -File "%ps1file%" %*
del "%ps1file%"
popd > nul
pause
exit /b %ERRORLEVEL%
# ========== do ps1 file as a dosbatch ==========

Write-Host "Hello ps1"
```

このファイルは、エクスプローラからダブルクリックで実行出来ます。
一時ファイルを作成してから実行しているのでダサいし、途中でスクリプトを強制終了すると一時ファイルが残ったままになるという致命的な問題はありますが、まぁちょっとしたスクリプトにはいいんではないでしょうか。

ちなみに、 neobundle の tap と untap とかの設定は、 supermomonga
さんの
[この記事](http://blog.supermomonga.com/articles/vim/neobundle-sugoi-setting.html)
がすごい参考になります。かなりすごいです。かなり。

さらにこの保存時に結合っていう方法を使えば、例えば先頭行にコメントで
import文みたいなのを入れておけば、動的に複数のpowershell
スクリプトを結合して、一つのバッチファイルにする・・・ってなこともできると思います。(誰かお願いします)

以上です。

