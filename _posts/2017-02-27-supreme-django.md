---
title: "Django最高"
date: "2017-02-27"
layout: "post"
---

北海道に帰ってきて1週間、バイトやらなんやらで充実した日々を送っています、Isaacです。

今回はDjangoのAdvancedなアレコレを書き留めます。

 

バイト先ではそこの性質上PHP一択ですが、私生活で私の身はPythonに染まりつつあります。フレームワークのDjangoは最高です。Pythonのシンプル且つパワフルな力を最大限に活かしたWebフレームワーク...もうゾッコンです。

先日参加したハッカソンでは、その力を借りてなんとかサービスの形に出来ましたが、まだまだ勉強不足な部分があって私自身が全然活かしきれてなかったので、最近勉強というかまた新たなサービスを作りながら勉強しています。

ちなみに今開発しているのはコレ👇

[https://github.com/i544c/schwul](https://github.com/i544c/schwul)

一人暮らしを始めるまでに完成させたいです...

 

ではこれまでに学んできたり、自分が学習する過程で身に付けた細々としたことを書き連ねていきます。

* * *

 

### DBに最初からデータを入れておきたい

調べてみたら、コマンドでDBをjsonにして書き出したり、読み込んだり出来るんですねぇ。先に/admin でDBにデータを作っておきます。

ではまずはデータをjsonで書き出します。

python manage.py dumpdata | python -mjson.tool > db.json

最初のパイプまででDBの中身を全て標準出力で出し、次でjsonを整形、最後にファイルに書き出す、というものです。非常に簡単ですね！これを知るまでdocker-compose up -d するたびに/admin でデータを作り直していました...

特定のAppのデータだけ出力するなら、

python manage.py dumpdata ${appname} | python -mjson.tool > ${filename}

となります。

 

では次にjsonから読み込ませます。

python manage.py loaddata ${filename}

特定のアプリだけの場合もjsonに上手いこと書いてあるのでこうなります。これまた非常に簡単。

 

今後も何か発見があったらココに追記していきます。