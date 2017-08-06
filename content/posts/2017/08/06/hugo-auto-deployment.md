---
title: "Git HooksでHugoのデプロイを自動化する"
date: 2017-08-06T14:01:16+09:00
draft: true
---

[公式ドキュメント](https://gohugo.io/hosting-and-deployment/deployment-with-wercker/)ではWerckerとGitHubを連携させてGitHub Pagesで公開する方法が紹介されている。
このブログはVPSに置いているので、外部サービスを使わずシンプルにGit Hooksで自動化してみる。

### 処理の流れ
1. ローカルで記事を書いてcommit
2. VPS上のbareポジトリにpush
3. VPS上のnon-bareリポジトリがbareリポジトリからpull
4. Hugoのビルドを実行

Git Hooks

### リポジトリの準備

#### サーバー側
まずはpush先となるbareリポジトリを作成する。

```
$ git init --bare path/to/bare.git
```

作成したbareリポジトリをcloneしてnon-bareリポジトリを用意する。

```
$ git clone path/to/bare.git non-bare
```

ローカルにあるHugoプロジェクトで`git init`してリモートのbareリポジトリをoriginに登録する。
```
$ git init
$ git remote add origin ssh://remote/path/to/bare.git
```

### Hookの作成
Git Hooksにはいくつか種類があるが、pushされたことをトリガーにして実行させたいので`post-receive`を使う。
Git Hooksは所定の名前のファイルをリポジトリの`hooks`ディレクトリに置くことで実行される(non-bareリポジトリの場合は`.git/hooks`)。

Hookでやりたいことは次の2つ。

- non-bareリポジトリに移動して更新をpullする
- Hugoのビルドを実行する

`post-receive`のスクリプトを設置。
実行権限が必要なので`chmod`を忘れずに。

```
$ cd path/to/bare.git/hooks
$ vim post-receive
$ chmod +x post-receive
```

#### 設置したスクリプトの中身

ブログの更新だけなのでかなり~~雑に~~シンプルにしている。

```
#!/bin/sh

cd /path/to/non-bare/
git --git-dir=.git pull origin master
hugo
```

### 実行結果
Hookを設置した状態でリモートにpushすると、次のようにログでHookの実行結果もわかる。

```

