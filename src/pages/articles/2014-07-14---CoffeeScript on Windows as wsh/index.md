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
date: "2014-07-14"
title: CoffeeScript on Windows as wsh
---

長い間、Windowsでcoffeescriptをwshとして記述する方法を模索していたけど、ようやく解が見つかった！！

[thinca/coffee-script-on-jscript](https://github.com/thinca/coffee-script-on-jscript)

thincaさん作成のcoffeescriptコンパイラ。Windowsで動く。nodejsもいらず、バッチファイル1つ `coffee.bat` と、公式の `coffee-script.js` だけで動く。


### 使用方法
```sh
> ghq get https://github.com/thinca/coffee-script-on-jscript.git
```

or

```sh
> git clone https://github.com/thinca/coffee-script-on-jscript.git
```

ghq があるなら上で。

後は普通のcoffeescriptコンパイラと同じオプションがだいたい使えるようなので、普通にコンパイルするのであれば

```sh
> coffee -c hello.coffee
```

とかで `hello.js` に変換される。

`watch` オプションもあるみたいなので、

```sh
> coffee -cw hello.coffee
```

としておくと、 `hello.coffee` を更新するたびに自動でコンパイルしてくれる。便利。

他のオプションは、 `coffee.bat` に何も引数をつけずに実行するとhelpで表示される。

```sh
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
```

### jscriptをバッチとして実行
上記までで、 `coffeescript` を `jscript` に変換することは簡単に出来るんだけど、どうせなら `jscript` からさらにバッチとして実行出来る状態に変換したい。 `jscript` をバッチとして実行するには、まさに `coffee.bat` がそうなっていて、ソースの一番最初に以下の4行を追加すればいいみたい。

```dos
@set @junk=1 /* vim:set ft=javascript:
@cscript //nologo //e:jscript "%~dpn0.bat" %*
@goto :eof
*/
```

そこで、`coffee.bat` に、コンパイル時に自動で上記4行を追加する処理を加えた。
(追加で戻り値も返すようにした。)

```javascript
function addHeader(file) {//{{{
  var content = binaryToString(readFile(file), "UTF-8");
  var header = "@set @junk=1 /* vim:set ft=javascript:\n@cscript //nologo //e:jscript \"%~f0\" %*\n@exit /b %errorlevel%\n*/\n\n";
  var cmd = file.replace(/(\.\w+)?$/, ".cmd");
  writeFile(cmd, (header + content).split("\n").join("\r\n"), "Shift_JIS");
}//}}}
```
また、 `jscript` として実行するために、coffeeからjsに変換されたファイルを UTF-8 で読み込んで、 Shift_JIS で出力するようにしてある。

例えば、以下の `hello.coffee` (文字コード: UTF-8 、 改行コード: LF で作成)があった場合、

- hello.coffee

```coffeescript
do (name = "CoffeeScript") ->
  WScript.Echo "はろー, \#{name} !"
```

次のようにコンパイルすると、

```sh
> coffee -c hello.coffee
```

`hello.js` (文字コード: UTF-8 、 改行コード: LF )
と `hello.cmd` (文字コード: Shift_JIS 、 改行コード: CRLF )が生成される。

- hello.js

```javascript
(function() {
  (function(name) {
    return WScript.Echo("はろー, " + name + " !");
  })("CoffeeScript");

}).call(this);
```

- hello.cmd

```javascript
@set @junk=1 /* vim:set ft=javascript:
@cscript //nologo //e:jscript "%~f0" %*
@exit /b %errorlevel%
*/

(function() {
  (function(name) {
    return WScript.Echo("はろー, " + name + " !");
  })("CoffeeScript");

}).call(this);
```

これでダブルクリックで簡単にバッチとして実行出来る `hello.cmd` が作成された。

一応、上記の `addHeader` 関数を加え、さらに `coffee-script.js` のバージョンを v1.7.1 にアップデートしたのを、thincaさんのからフォークして以下にあげた。

[yukimemi/coffee-script-on-jscript](https://github.com/yukimemi/coffee-script-on-jscript)

Windowsではこれからは PowerShell だ！と思ってたけど、やっぱwshもまだまだ使えそう・・・。
ExcelとかのCOMも、PowerShellより、wshの方が断然早いし。というかPowerShellはなぜあんなに遅いんだろうか・・・。

- - -

##### 参考
[JScript で動く CoffeeScript コンパイラのラッパ書いた - 永遠に未完成](http://d.hatena.ne.jp/thinca/20110707/1310014720)

[thinca/coffee-script-on-jscript](https://github.com/thinca/coffee-script-on-jscript)

