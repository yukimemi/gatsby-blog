---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- wsh
Description: ""
Tags:
- wsh
- coffeescript
- jscript
- windows
date: "2014-07-15"
title: Add --runcmd option to coffee-script-on-jscript
---

先日の [記事](http://yukimemi.github.io/posts/2014-07-14_CoffeeScript%20on%20Windows%20as%20wsh.html) のthincaさんフォーク版、僕々仕様のWindows上でのCoffeeScriptコンパイラに `--runcmd` オプションを加えた。

[yukimemi/coffee-script-on-jscript](https://github.com/yukimemi/coffee-script-on-jscript)

```dos
> coffee
Usage: coffee [options] path/to/script.coffee

  -b, --bare         compile without the top-level function wrapper
  -c, --compile      compile to JavaScript and save as .js files
      --encoding     character encoding used by source files
  -e, --eval         compile a string from the command line
  -h, --help         display this help message
  -j, --join         concatenate the scripts before compiling
  -n, --nodes        print out the parse tree that the parser produces
  -o, --output       set the directory for compiled JavaScript
  -p, --print        print the compiled JavaScript to stdout
  -s, --stdio        listen for and compile scripts over stdio
  -t, --tokens       print the tokens that the lexer produces
  -v, --version      display CoffeeScript version
  -w, --watch        watch scripts for changes, and recompile
  -r, --runcmd       run the compiled scripts as JScript
```

最後の行のやつ。

オプションで `-r` もしくは、 `--runcmd` を指定すると、自動でコンパイルされて実行形式 (\.cmd) になったファイルが実行される。

- hello.coffee

```coffeescript
do (name = "CoffeeScript") ->
  WScript.Echo "はろー, \#{name} !"
```

上記の ```hello.coffee``` を、文字コード: UTF-8 改行コード: LF で作成し、次のようにコンパイル。

```dos
> coffee -r hello.coffee
はろー, CoffeeScript !

ExitCode = [0]
```

自動でコンパイルしてJScriptとして実行される。(あと一応戻り値もEchoするようにしておいた。)

`--watch` オプションと一緒に使うとかなり便利。

```dos
> coffee -rw hello.coffee
はろー, CoffeeScript !

ExitCode = [0]


へろー, CoffeeScript !

ExitCode = [0]
```

保存する度に自動でコンパイル、実行まで行ってくれる。

便利なのはいいんだけど、今どきJScriptとか、時代に逆行してるなぁ・・・。

- - -

##### 参考
[Exec メソッド](http://msdn.microsoft.com/ja-jp/library/cc364356.aspx)

