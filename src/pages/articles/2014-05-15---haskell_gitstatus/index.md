---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- haskell
Description: ""
Tags:
- haskell
- git
date: "2014-05-15"
title: haskellでgitstatus
---

前に Go言語で [「すべてのディレクトリで git statusする」](http://yukimemi.bitbucket.org/blog/html/2014/03/22/gitstatus.html#more)
という記事を書いた。

今度は、 haskell で同じようなのを書いてみた。

```haskell
import System.IO
import System.Process
import System.Directory
import Control.Exception
import System.FilePath

getAllFiles :: FilePath -> IO [String]
getAllFiles dir = do
  contents <- getDirectoryContents dir `catch` (\(SomeException e) -> const (return []) e)
  let contents' = [dir </> path | path <- contents, notElem path [".", ".."]]
  contents'' <- mapM getAllFiles contents'
  return $ contents' ++ concat contents''

takeLast list n = reverse . take n $ reverse list

join :: String -> [String] -> String
join sep [] = []
join sep [x] = x
join sep (x:xs) = x ++ sep ++ join sep xs

onlyGitDir :: FilePath -> Bool
onlyGitDir fileName =
  if last == ".git"
    then True
    else False
      where last = takeLast fileName 4

gitStatus :: FilePath -> IO String
gitStatus path = do
  setCurrentDirectory path
  readProcess "git" ["status"] ""

printGitStatus :: FilePath -> IO ()
printGitStatus path = do
  putStrLn "--------------------------------------------------------------------------------"
  putStrLn $ "* " ++ path
  putStrLn "--------------------------------------------------------------------------------"
  gitStatus path >>= putStrLn
  return ()

main = do
  path <- getCurrentDirectory
  files <- getAllFiles path
  let dirs = map takeDirectory $ filter onlyGitDir files
  mapM printGitStatus dirs
  return ()
```

haskell は勉強始めたばっかだからいろいろ改善の余地があると思われる。
詳しい人教えてくだしあ

##### 参考:

[HaskellでOSコマンド実行 - yunomuのブログ](http://yunomu.hatenablog.jp/entry/2012/03/10/111222)

[Haskell - 移植性の問題(ファイル名問題(パス区切りの違い), System.FilePathモジュール(pathSeparator, joinPath関数)) | Kamimura's blog](http://sitekamimura.blogspot.jp/2014/01/haskell-systemfilepathpathseparator.html)

[Haskell - すてきにへんな覚え書き](http://tsurushuu.wikidot.com/haskell)

