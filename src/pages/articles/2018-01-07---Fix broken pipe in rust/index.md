---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- rust
Description: ""
Tags:
- rust
date: "2018-01-07T16:59:47+09:00"
title: Fix Broken Pipe in Rust
---

[以前の記事](http://yukimemi.github.io/post/2017-11-12_gsr-in-rust/) で、 rust で変更のある `git` リポジトリを再帰的に検索する、というのを書いたのだけど、一部バグがあったので、修正した。

`gsr` では、 `--all` というオプションを指定することで、指定配下 (デフォルトは $(ghq root) ) の git リポジトリを全て列挙する。
それに、パイプで `fzf` や、 `peco` を通すことで、選択して移動、というのをやっていた。

`fish` で書くとこんな感じ。

```fish
function __cd_to_git_dir
  gsr --all | peco | read -l line
  and echo "Change directory $line"
  and cd $line
end
```

だけど、 `gsr` が全ての結果を出力する前に、選択を決定して移動してしまった場合、以下のエラーが出力された。

```sh
thread 'main' panicked at 'failed printing to stdout: Broken pipe (os error 32)'
```

どうやら、 `println!` で標準出力に出力している場合で、パイプ出力が中断されるとダメみたい。

簡単のために、以下の `rust` プログラムで実験。


```rust
fn main() {
    (0..100).into_iter().map(|x|
        println!("Hello: {}", x)
    ).collect::<Vec<_>>();
}
```

このプログラムに対して、 `head` を通すとエラーが再現する。

```sh
$ cargo run | head
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/pipe-test`
Hello: 0
Hello: 1
Hello: 2
Hello: 3
Hello: 4
Hello: 5
Hello: 6
Hello: 7
Hello: 8
Hello: 9
thread 'main' panicked at 'failed printing to stdout: Broken pipe (os error 32)', src/libstd/io/stdio.rs:690:9
note: Run with `RUST_BACKTRACE=1` for a backtrace.
```

これを直すには、以下のように、 `writeln` を使用して、エラー時はちゃんとプロセスを終了するようにするといいみたい。
(ただ、これが正しい解決方法なのかはわからんです・・・。)

```rust
use std::io::{self, Write};
use std::process;

fn main() {
    (0..100)
        .into_iter()
        .map(|x| {
            let r = writeln!(&mut io::stdout(), "Hello: {}", x);
            if r.is_err() {
                // Probably a broken pipe. Exit gracefully.
                process::exit(0);
            }
        })
        .collect::<Vec<_>>();
}
```

これで、再度実行すると、正しく終了する。


```sh
$ cargo run | head
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/pipe-test`
Hello: 0
Hello: 1
Hello: 2
Hello: 3
Hello: 4
Hello: 5
Hello: 6
Hello: 7
Hello: 8
Hello: 9
```

- - -

##### 参考

[Panic on broken pipe #24](https://github.com/sharkdp/fd/issues/24)

[Exit gracefully on broken pipe, fixes #24](https://github.com/sharkdp/fd/commit/2ea23c00005602255a29e387006a9fc1e91185b1)

