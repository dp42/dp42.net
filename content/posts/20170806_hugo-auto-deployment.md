---
title: "Git HooksでHugoのデプロイを自動化する"
slug: "hugo-auto-deployment"
date: 2017-08-06T14:01:16+09:00
---

[公式ドキュメント](https://gohugo.io/hosting-and-deployment/deployment-with-wercker/)ではWerckerとGitHubを連携させてGitHub Pagesで公開する方法が紹介されている。  
このブログはVPSに置いているので、外部サービスを使わずシンプルにGit Hooksで自動化してみる。

### 処理の流れ
1. ローカルで記事を書いてcommit
2. VPS上のbareポジトリにpush
3. VPS上のnon-bareリポジトリがbareリポジトリからpull
4. Hugoのビルドを実行

Git Hooksを使って手順3と4を自動化する。  
人間がやるのは記事を書いてpushすることだけ。

### リポジトリの準備

#### サーバー側
まずはpush先となるbareリポジトリを作成する。

```
$ git init --bare path/to/bare.git
```

作成したbareリポジトリをcloneしてnon-bareリポジトリを用意する。  
Hugoのビルドはここでやる。
```
$ git clone path/to/bare.git non-bare
```

ローカルにあるHugoプロジェクトで`git init`して、リモートのbareリポジトリをoriginに登録する。
```
$ git init
$ git remote add origin ssh://remote/path/to/bare.git
```

### Hookの作成
Git Hooksは所定の名前のファイルをリポジトリの`hooks`ディレクトリに置くことで実行される(non-bareリポジトリの場合は`.git/hooks`)。
今回はpushされたことをトリガーにして実行させるので`post-receive`を使う。

ref. [Git - Git フック](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%9E%E3%82%A4%E3%82%BA-Git-%E3%83%95%E3%83%83%E3%82%AF)

Hookでやりたいことは次の2つ。

- non-bareリポジトリに移動して更新をpullする
- Hugoのビルドを実行する

`post-receive`のスクリプトを設置する。  
実行権限が必要なので`chmod`を忘れずに。

```
$ cd path/to/bare.git/hooks
$ vim post-receive
$ chmod +x post-receive
```

スクリプトの中身は下記。  
ブログの更新だけなので処理はかなり~~雑に~~シンプルにしている。

```
#!/bin/sh

cd /path/to/non-bare/
git --git-dir=.git pull origin master
hugo
```

### 実行結果
Hookを設置した状態でリモートにpushするとHookの実行結果もターミナルに出力される。

```
$ git push origin master
Counting objects: 8, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (8/8), 864 bytes | 0 bytes/s, done.
Total 8 (delta 2), reused 0 (delta 0)
remote: From ***************
remote:  * branch            master     -> FETCH_HEAD
remote: Updating 1e17a9a..6e47c7b
remote: Fast-forward
remote:  content/posts/2017/08/06/hugo-auto-deployment.md | 18 ++++++++++++++++++
remote:  1 file changed, 18 insertions(+)
remote: Started building sites ...
remote: Built site for language en:
remote: 0 of 1 draft rendered
remote: 0 future content
remote: 0 expired content
remote: 1 regular pages created
remote: 8 other pages created
remote: 0 non-page files copied
remote: 0 paginator pages created
remote: 0 tags created
remote: 0 categories created
remote: total in 7 ms
To ssh://************
   1e17a9a..6e47c7b  master -> master
```

### まとめ
シェルスクリプト置くだけでよいので非常に楽だった。  
自動化しても記事を書かなきゃ意味が無いのでアウトプットの習慣化を頑張ろう。
