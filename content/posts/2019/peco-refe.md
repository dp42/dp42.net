---
title: "refeとpecoを連携させてるりまをインクリメンタルに引く"
slug: "peco-refe"
date: 2019-01-27T12:00:25+09:00
---

Rubyの日本語リファレンスマニュアル(通称るりま)にはrefeというターミナルからマニュアルを閲覧するためのツールが用意されている。
うろ覚えのメソッドもインクリメンタルにスッと調べられるようpecoと連携させた。

### 準備
```bash
gem install refe2 # refeのインストール

bitclust setup    # マニュアルデータベースのセットアップ

brew install peco # pecoのインストール

brew install bat  # batはシンタックスハイライト機能を持ったcatクローン
                  # 出力結果がハイライトされて見やすくなるし、
                  # 長いファイルのときはページャも自動で起動してくれる(デフォルトはless)
```

#### インデックスの作成
refeのバックエンドで動いてるbitclustにはlistというサブコマンドがあり、エントリのリストを出力できるので、
テキストファイルに出力しておく。これをpecoに渡してインクリメンタルサーチのインデックスに使う。

```bash
bitclust list --library --class --method >> ~/ruby_dict.txt
```

#### fishの関数を用意
シェルはfishを使っているので、コマンド一発で実行できるように自作関数を用意しておく。

##### 'peco_refe.fish'
```
function peco_refe
  refe (cat ~/ruby_dict.txt | peco) | bat -l ruby
  end
```

### 実行結果

![peco_refe](/images/peco_refe.gif)
