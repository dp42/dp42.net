---
title: "Hugoのテーマを自作した"
slug: "0116-my-hugo-theme"
date: 2019-01-16T09:00:00+09:00
---

若い頃からの躁鬱にに限界が来て去年から無職してるのだけど、
最近やっと気分の波が落ち着いてきたのでリハビリがてらにHugoのテーマを自作した。
お題として次の2つだけ決めてほとんどノープランでスタート。

1. シングルカラム
1. スタートレックのLCARS風

出来上がったものはこちら

- [dp42/final-frontier: Dark colored hugo theme inspired by LCARS](https://github.com/dp42/final-frontier)


### シングルカラム
サイドバーがごちゃついてるレイアウトが好みでないのでシングルカラムはマスト。

### スタートレックのLCARS風
最初はSVGでそれっぽいパーツ作ったり、CSSでアニメーションさせたりしてLCARSの再現重視でやってたけど、
やりすぎると本文が読みにくくなって本末転倒になったので、
結局は配色と角丸だけでなんとなくそれっぽい雰囲気を出すにとどめた。

#### 配色
"LCARS"で画像検索していだくと分かるけれど、
オリジナルの配色は割とどぎつくて、ブログで使うにはちょっと厳しい。
なのでvimで愛用しているカラースキーム"Iceberg"のカラーパレットを参考にさせてもらった。

- [cocopon/iceberg.vim: Dark blue color scheme for Vim and Neovim](https://github.com/cocopon/iceberg.vim)

### ふりかえり 
#### ノープランで始めない
落書き程度でも完成イメージを用意したほうが良かった。
ノープランだとゴールが見えなくて迷走する。
ちょっと修正しては見た目を確認という作業を延々やることになったので反省。

#### ダミー記事を大量生成しておく
自前の記事の数が非常に少ないので、ページネーションや記事リストの表示確認にダミー記事を大量に生成した。


```bash
#!/bin/bash

for i in `seq -w 1 100`; do

hugo new content/posts/2100/$i.md

cat << EOS >> ./content/posts/2100/$i.md
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
doneUt enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
doneDuis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
EOS

done
```

### TODO
- README書く
- 細かいスタイルがおざなりなのでもうちょっと作り込む
- メニュー用意したりOGPの対応もやる
- せっかくなので[Hugo Themes](https://themes.gohugo.io/)への登録目指す
