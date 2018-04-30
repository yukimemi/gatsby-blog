---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- fish
Description: ""
Tags:
- fish
- zsh
- fisherman
- fisher
date: "2016-11-27T00:49:25+09:00"
title: Use fish instead of zsh
---

ちまたで(？)話題の `fish` を使ってみた。

長年使ってきた `zsh` のかわりにはならないだろうなーと思いながら・・・。


- - -

・・・が、いざ使ってみると非常に使いやすい！

`zsh` のかわりになるどころか置き換えてしまった。  
(つまり、自分は結局そこまで `zsh` を使いこなしていなかったんだろう・・・。)

`fish` のチュートリアルは、以下にある。

[fish tutorial](https://fishshell.com/docs/current/tutorial.html)

日本語でもqiitaとかにいっぱい解説記事があるので難しいことはないと思う。
唯一はまったのは、上記tutorialにある、 `hybrid_bindings` 。

vi っぽいキーバインドで、 insert モードの場合は emacs っぽくするって感じなんだろうけど、使用できなかった。
なんか issue 上がってたりしたので、そのうち修正されるのかもしれない。

とりあえずは、必要なキーバインドのみを自分で再定義してやることで要望は満たせた。

`fish` では、 [fisherman](https://github.com/fisherman/fisherman) というプラグインマネージャーが存在しており、これを使って各種プラグインを管理する。
日本語での解説も書いてあるので導入は簡単。

```sh
$ curl -Lo ~/.config/fish/functions/fisher.fish --create-dirs git.io/fisher
```

上記を実行するだけ。

自分が入れたパッケージはとりあえずこんだけ。

- `simple` :
	シンプルなテーマ
- `omf/gi` :
	gitignore を管理できるやつ。

2個だけ。 `fish` が単体で色々出来るやつだからこそかも。

`fish` は、設定を `~/.config/fish` 配下で管理する。
こんな感じ。

```sh
$ tree fish
fish
├── completions
├── conf.d
├── config.fish -> /Users/yukimemi/.dotfiles/.config/fish/config.fish
├── fishd.xxxxxxxxxxxx
├── fishfile -> /Users/yukimemi/.dotfiles/.config/fish/fishfile
├── functions
└── my_functions -> /Users/yukimemi/.dotfiles/.config/fish/my_functions
```

```sh
$ tree my_functions
my_functions
├── __cdup.fish
├── __filter_command.fish
├── __filter_command_execute.fish
├── __filter_command_history_execute.fish
├── __filter_command_history_select.fish
├── __filter_command_select.fish
└── fish_user_key_bindings.fish
```

それから、上記 `fisherman` を使っている場合、 `~/.config/fish/functions` 配下がどんどん侵されていくので、自分で作成した `function` は `my_functions` というディレクトリ配下で管理することにした。
(シンボリックリンクが嫌な場合、それを解決した [fresco](http://qiita.com/masa0x80/items/142bc668ea8e5084ce7c) というものもあるらしい。)

`config.fish` から、 `my_functions` の内容を読み込む。

- `~/.config/fish/config.fish`

```sh
# Load my_functions.
for func in ~/.config/fish/my_functions/*.fish
  source $func
end
```

かんたん♪

あとは、 `peco` や `fzf` などのフィルター系コマンドが使えるようにする。
zsh の時は、`fzf` を使っていたんだけど、 `fzy` ってのがあって、 `enhancd` でおすすめって書いてあったから、とりあえずそれを使ってみる。

まずは、コマンド定義。

- `~/.config/fish/my_functions/__filter_command.fish`

```sh
function __filter_command
  fzy -l 200
end
```

`fzy` を使うよってだけ。 `-l` オプションは表示する候補数。

それから、これを使って、選択だけするやつと、実行するやつを定義。

- `~/.config/fish/my_functions/__filter_command_select.fish`

```sh
function __filter_command_select
  __filter_command | read -l line
  and commandline $line
end
```

- `~/.config/fish/my_functions/__filter_command_execute.fish`

```sh
function __filter_command_execute
  __filter_command | read -l line
  and echo "
  ------
  Running command: $line
  ------"
  and eval $line
  commandline -f repaint
end
```

今度はこれで履歴検索するやつを作る。

- `~/.config/fish/my_functions/__filter_command_history_select.fish`

```sh
function __filter_command_history_select
  history | sort -u | __filter_command_select
end
```

- `~/.config/fish/my_functions/__filter_command_history_execute.fish`

```sh
function __filter_command_history_execute
  history | sort -u | __filter_command_execute
end
```

こんな感じ。
簡単でわかりやすい。

あとはこれをキーに割り当てる。
`fish` では、キーバインドは `fish_user_key_bindings` という関数で行うのが通例っぽい。

- `~/.config/fish/my_functions/fish_user_key_bindings.fish`

```sh
function fish_user_key_bindings

  bind -M insert \cf accept-autosuggestion
  bind -M insert \cn down-or-search
  bind -M insert \cp up-or-search
  # TODO: Not work.
  bind -M insert \c\^ __cdup

  # filter command.
  bind -M insert \cr __filter_command_history_select
  bind sul __filter_command_history_execute

end
```

`-M` オプションでモードを指定できる。

べんりー！
あとは、前使ってた `enhancd` を使いたい。
それから、 `C-^` で親ディレクトリに移動するってのを `zsh` のときにやってたんだけど、それが `fish` だとうまくできない。

どうやってやればいいんだろ・・・。知ってる人いたら教えてください。

- - -
##### 参考

[fish - Documentation](https://fishshell.com/docs/current/index.html)

[初心者がShellを知りFish〜Fishermanを導入するまで](http://qiita.com/nutsinshell/items/5f111184b50f7081c92f)

[fish-shell と fisherman の tips](http://qiita.com/sotayamashita/items/61d49431053c44f01714)

