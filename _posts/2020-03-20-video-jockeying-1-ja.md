---
layout: post
title:  "VJ について 1"
author: naoto
categories: [ report ]
tags: [ essay, japanese ]
image: assets/images/2020-03-20-video-jockeying-1-ja.png
description: "雑感"
featured: true
comments: true
---

現在ドルトムントのシアターで Unity を使ってオーディオビジュアル作品の制作を手伝っているのですが、ネタに困っていたので2年前に韓国のソウルでレジデンスをした際の自分の作品を久々に見てみました。この作品はカナダでいっしょに活動をしていた Evelyne Drouin との共作で、彼女の曲に合わせて Processing で VJ （ビデオ・ジョッキー）をしました。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">2年前に韓国でやった VJ （VJ らしい VJ はこの時が最初で最後な気がする）、Processing で全部書いたけど今見るとばっちりオーディオリアクティブに作りこまれててよくできてた。そのうち機会があったら再利用したいな（動画は音無しです） <a href="https://t.co/8e8rCh8tSm">pic.twitter.com/8e8rCh8tSm</a></p>&mdash; Naoto HIÉDA (@naoto_hieda) <a href="https://twitter.com/naoto_hieda/status/1240755536196501504?ref_src=twsrc%5Etfw">March 19, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

それを Twitter にも貼ったら少し伸びていてうれしかったのですが、この作品以降は発表する場がなく VJ はしていません。この作品は Processing 上で JavaScript バインディングを用いた独自のフレームワークを使っていて、音の入力は用いずに BPM 同期と MIDI コントローラだけでオーディオ・リアクティブに見せています。音楽は Ableton Live で制作されていたので初めはネットワーク上で通信させることも考えたのですが、それはせずに開始のタイミングだけあわせてキューのリストを作って映像のプログラムを切り替えるようにしました。映像は3つのレイヤーがあって、Hydra をベースに作ったフラグメント・シェーダ、ジェネラティブな 3D モデル、そして下のシンプルな線と点の順に重ねた後にシェーダでポストエフェクトをかけています。

<blockquote class="twitter-tweet" data-conversation="none"><p lang="ja" dir="ltr">この頃の自分本当に VJ やりたかったのが伝わってくる、最近はもう諦めてたけどまた挑戦してみようかな <a href="https://t.co/ssfZeoPoPy">pic.twitter.com/ssfZeoPoPy</a></p>&mdash; Naoto HIÉDA (@naoto_hieda) <a href="https://twitter.com/naoto_hieda/status/1240757881403772934?ref_src=twsrc%5Etfw">March 19, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

制作をしていて勉強になったのはビデオ・ループなどと違い、ジェネラティブな表現を一から作って VJ をする難しさです。Twitter にモーション・グラフィックス系の動画を上げる場合は最低でも3秒あればいいですが、それを10分の VJ にするだけでも200倍に引き延ばさなければなりません。そしてただランダムに流せばいいのではなくある程度ストーリー性がないとおもしろくないし、「ただレイヤーを重ねている」だけではない複雑な表現をしないと単調に感じてしまいます。その点、モーション・グラフィックスではなくゆっくりとパーティクルを動かすような表現（個人的にはスクリーンセーバー系と呼んでいます）は尺は埋められますがビートと同期させるのは苦手です。

3D モデルやシェーダはある程度プリセットに沿いながらもランダムに表示するようにしましたが、手前の点と線のレイヤーは時間をかけて音との関連性が出るようにしました。例えば周波数が倍音にまとまっている音は一本の曲線、周波数がばらけているノイズに近い音は線ではなく点というように、音色に合わせていくつかモーション・グラフィックスのパターンを作ったため、音の波形そのものとは全く別にデザインしています。これは以前、レーザーを使ったオーディオビジュアル・パフォーマンスをしている Robert Henke のトークを聞いたときに、レーザーに入力する波形をそのまま鳴らした場合、円のパターンの音はサイン波になり知覚的には関連性がつかめないのでレーザーと音を別個に作っていると話していたことに影響を受けたためです。

このような設計をしていく中で、自分で音を作ればより深いレベルでのオーディオ・リアクティブな映像が作れるのではないかと感じるようになったことや、それと同時に映像を一つ一つコードで作る効率の悪さをもどかしく感じるようになりました。そしてキューやシーンが増えると転換の際にプログラムのパフォーマンスが落ちないような実装をしなければならず、コードが余計に複雑になります。そのような理由から以降は VJ に苦手意識を持っています。最近では[サウンドとビジュアルの実験]({{ site.baseurl }}/sound-texture-study-30/)をしていますが、どれも短いスケッチのためパフォーマンスに使えるようなものではありません。それでも今一度 VJ に挑戦したいという気持ちが捨てきれないので、今後気が向いたらブログに映像を多めに交えつつ進捗を書きながら p5.js 等を用いてオーディオビジュアル用のフレームワークを作ろうと考えています。

<!-- Hydra 独自の文法であるため最終的なシェーダのソースにアクセスできないだけでなく、フレームバッファやシーケンサなど JavaScript 側の処理があるため、シェーダをインポートするのはスムーズにはいきませんでした。結局 Hydra サーバのソースを編集してシェーダをコンソールに出力するようにして、複数のフレームバッファのサポートや uniform 変数のシーケンサは Processing 側で一から実装しました。

また、レイヤーの重ね方やジェネラティブな図形の動きについてはプリセットを JSON 形式で保存していくつか組み合わせたり、ランダムでパラメータを選んだりできるようにしてなるべく単調にならないように工夫をしました。

[https://github.com/micuat/sasg/blob/master/mgtk/js/sketch.js](https://github.com/micuat/sasg/blob/master/mgtk/js/sketch.js)
 -->