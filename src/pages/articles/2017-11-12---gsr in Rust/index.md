---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- rust
Description: ""
Tags:
- gsr
- rust
date: "2017-11-12T10:27:29+09:00"
title: gsr in Rust
---

以前の [投稿](http://yukimemi.github.io/post/2017-03-19_git-status-recurse/) で、`git status を再帰的に全Gitディレクトリで実行して、変更のあるリポジトリを簡単に見つける` というのを `golang` で書いていたのだが、それを `rust` で書き直してみた。

[yukimemi/gsr-rs](https://github.com/yukimemi/gsr-rs)

インストールは、 `cargo` で実施。

```sh
$ cargo install --git https://github.com/yukimemi/gsr-rs
$ gsr -h
gsr 0.2.2

USAGE:
    gsr [FLAGS] [input]

FLAGS:
        --ahead      Print ahead repo
    -a, --all        Print all git directory
        --behind     Print behind repo
    -f, --fetch      Execute git fetch before check
    -h, --help       Prints help information
    -V, --version    Prints version information

ARGS:
    <input>    Input directory. default is $(ghq root) or '.'
```

使い方は、だいたい [以前の投稿](http://yukimemi.github.io/post/2017-03-19_git-status-recurse/) で書いた `golang` 版と同じ。

いくつかライブラリを使っていて、便利だったものを紹介。

### [structopt](https://github.com/TeXitoi/structopt)

使い方は、 github 上の [README](https://github.com/TeXitoi/structopt) を読めばすぐわかると思う。

[yukimemi/gsr-rs](https://github.com/yukimemi/gsr-rs) では、以下のようにしてオプションの定義を実施。


```rust
#![feature(attr_literals)]
extern crate structopt;
#[macro_use]
extern crate structopt_derive;

use structopt::StructOpt;

#[derive(StructOpt, Debug)]
struct Opt {
    #[structopt(short = "a", long = "all", help = "Print all git directory")]
    all: bool,

    #[structopt(short = "f", long = "fetch", help = "Execute git fetch before check")]
    fetch: bool,

    #[structopt(long = "behind", help = "Print behind repo")]
    behind: bool,
    #[structopt(long = "ahead", help = "Print ahead repo")]
    ahead: bool,

    #[structopt(required = false, help = "Input directory. default is $(ghq root) or '.'")]
    input: Option<String>,
}
```

これで、上記で書いたようないい感じの help が表示される。
内部的に [clap](https://github.com/kbknapp/clap-rs) を使ってるみたい。

### [walkdir](https://github.com/BurntSushi/walkdir), [rust-threadpool](https://github.com/rust-threadpool/rust-threadpool)

ディレクトリやファイルを再帰的に取得するやつ。
だいたい cli でなんかするときには必要な気がする。
[yukimemi/gsr-rs](https://github.com/yukimemi/gsr-rs) では、以下のようにして、 git のディレクトリをリスティングしている。

```rust
fn get_rootdir(input: &Option<String>) -> WalkDir {
    match *input {
        Some(ref inp) => WalkDir::new(inp),
        None => {
            if let Ok(out) = Command::new("ghq").arg("root").output() {
                return WalkDir::new(String::from_utf8_lossy(&out.stdout).trim_right());
            }
            WalkDir::new(".")
        }
    }
}

fn get_gsrs(walk_dir: WalkDir, fetch: bool) -> mpsc::Receiver<Gsr> {
    let (tx, rx) = mpsc::channel::<Gsr>();
    let pool = ThreadPool::new(WORKERS);
    thread::spawn(move || {
        walk_dir
            .into_iter()
            .map(|e| match e {
                Ok(e) => {
                    if e.file_name().to_str().unwrap_or("").eq(".git") {
                        let tx = tx.clone();
                        pool.execute(move || {
                            let parent = e.path().parent().unwrap();
                            let gsr = Gsr::new(parent);
                            if fetch {
                                gsr.fetch();
                            }
                            let gsr = gsr.status().diff().is_ahead().is_behind();
                            tx.send(gsr).unwrap();
                        });
                    }
                }
                Err(e) => eprintln!("{}", e),
            })
            .collect::<Vec<_>>();
        pool.join();
        drop(tx);
    });
    rx
}
```

ここでは、ディレクトリリスティングと、 git status などの取得をパラレルで行うために、 [rust-threadpool](https://github.com/rust-threadpool/rust-threadpool) も使用している。

この `crate` も非常に便利だった。

- - -

##### 参考

[structopt](https://github.com/TeXitoi/structopt)

[walkdir](https://github.com/BurntSushi/walkdir)

[rust-threadpool](https://github.com/rust-threadpool/rust-threadpool)

