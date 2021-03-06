---
layout: post
title:  "Temps d'Images 2020"
author: naoto
categories: [ report ]
tags: [ essay, japanese ]
image: assets/images/2020-01-18-temps-d-images-2020-ja.jpg
description: "Temps d'Images のレポート"
featured: true
comments: true
---

ドイツのデュッセルドルフで2020年1月10日から18日に行われた[Temps d'Images](https://tanzhaus-nrw.de/en/specials/festival/temps-dimages-2020)のレポートです。

このフェスティバルには昨年は Choy Ka Fai の Dance Clinic のインタラクティブ・プログラミングで参加させていただいた他、代打でプレゼンテーションもさせていただきました。今回は Eleonora Siarava の The Body and the Other にてリアルタイムのプロジェクション・マッピングの担当として参加しました。公演のほかインスタレーションもありましたが、インスタレーションでもパフォーマーと踊りながら体験するものが多かったため残念ながら写真はほとんど撮れていません。


Showing with works by Alexander Rechberg, Eleonora Siarava und Charlotte Triebus
--------

<https://tanzhaus-nrw.de/en/event/2020/01/showing>

アーティスト・イン・レジデンスの形で三組のアーティストがそれぞれ制作したものの発表で、この枠で Eleonora Siarava と制作をしました。Alexander Rechberg はケルンで仲良くしているアーティストなのですが、ダンサーの Yurika Yamamoto にプロジェクション・マッピングのような形でレーザーを当てたり壁に図形を描いたりしていました。一週間の制作期間だったため粗削りな印象はありましたが、レーザーで描いた円に沿うようにゆっくりとした動きで腕を伸ばしたり、かと思えば乱雑な曲線を体と壁に当てたりと技術とダンスの両面から実験していた様子が分かりました。なお、音はレーザーにマイクをつけてミラーの音を増幅して使用していたそうです。

Eleonora Siarava の作品は、ストラクチャーのあるインプロビゼーションを繰り返しながら徐々に動きやエネルギーを変えていくというコンセプトで非線形的な時間や空間を作り出すためにプロジェクション・マッピングでステージの後ろにあるいくつかの幕にディレイ・フィルターを通したライブ映像を投影しました。初めは Processing を用いていたのですが、遅延させる際にバッファがなぜか GPU だけでなく CPU 側のメモリを消費する jogl の仕様がボトルネックになったため、直前に一部を openFrameworks の ofTexture で保存するように書き換えて本番に用いました。振付家やダンサーの他、今回はデュッセルドルフの工科大学である Hochschule Düsseldorf と共同で制作しました。


Stéphane Gladyszewski: Tête-à-Tête
--------

<https://tanzhaus-nrw.de/en/event/2020/01/stephane-gladyszewski-0>

フランス語から直訳すると「頭と頭」というタイトルのように、観客一人とダンサー一人が一対一で行う体験です。初めは席についてマスクに顔を当てて目の部分からのぞき穴のようにステファンが火をつけて流しに捨てたりリンゴを食べたり、濡れたライスペーパーを顔にマスクのようにつけるのを見るのですが（控室で大量のライスペーパーを見つけたのですがこのためだったようです）、終盤にスペースシャトルの打ち上げの音声と同時にマスクが持ち上げられ、ステファンと顔を突き合わせる形になります。とても目力が強く、ステファンが退場する際も最後までこちらと目を合わせているのですが、体験者としても試される作品でした。


Stéphane Gladyszewski: Corps noir
--------

<https://tanzhaus-nrw.de/en/event/2020/01/stephane-gladyszewski>

こちらはステファンのステージ・パフォーマンスで、自らの出生をテーマにした作品です。セットの世界観は Tête-à-Tête と同じようなイメージで、ステージ上にはクーラーボックスや水たばこ、水槽、粘土などウェットな素材が多く配置されていてそれを用いるパフォーマンスなのですが、かと思えばサーモグラフィを使った精巧な動体へのプロジェクション・マッピングも行っておりとても完成度が高く感じられました。


Noah Hellwig: Multiplex Realities
--------

<https://tanzhaus-nrw.de/en/event/2020/01/noah-hellwig>

VR インスタレーションというカテゴリなのですが、二つの体験に分かれており片方のみ VR で、どちらもダンサーと会話しながらストーリーが進みます。私はまず非 VR の体験をしました。ダンサーのディサに手を取られ、今自分の部屋にいてミーティングに出かけなければならない、等とシチュエーションを伝えられます。それから質問に答えたり部屋の中を歩き回る中で想像を膨らませながら話が進みます。

VR の体験ではダンサーのノアに促されるように部屋の中でテーブルの上を探ったり実在する箱を動かしたりします。そのうちに壁が見えるものの手で触れなかったり、箱が VR の映像では見えないけれども触ることができることに気づきます。そしてノアに「どうしてあなたは実在しているといえるの？」と質問をされるのですが、答えに窮しつつも「自分は存在しないのかもしれないし、存在するかもしれない」と答えました。後でノアと話したところこのような切り返しをする人は珍しいと言われてうれしかったです。


Éric Minh Cuong Castaing & Xavier Boissarie: Vibes
--------

<https://tanzhaus-nrw.de/en/event/2020/01/eric-minh-cuong-castaing-xavier-boissarie>

ダンサー・振付家のエリックがメディア・アーティストやプログラマーとハッカソンをきっかけに作った作品で、参加者はそれぞれスマートフォンを渡され、イヤホンで指示されたとおりに動くダンス作品です。サイレント・ディスコのダンス・ワークショップ版といった感じなのですが、ワークショップとは違い誰か一人がリードすることなく、かといってコンタクト・インプロヴィゼーションのジャムのように各組が別々に動くようなこともなく新鮮な体験でした。エリックは産業用ロボットを用いた振付や最近は障害のある子どもとパフォーマンスをしていたり、奇抜でありながらよく練られた作品を作っていてとても好きです。


Choy Ka Fai & Mária Júdová: Constellation of the flesh
--------

<https://tanzhaus-nrw.de/en/event/2020/01/choy-ka-fai-maria-judova>

去年のフェスティバルでは一緒に仕事させてもらったカーファイですが、今回は DUST 等の VR ダンス作品で注目を集めている Mária Júdová とコラボレーションした VR パフォーマンスを発表していました。カーファイは現在制作中の Cosmic Wander のリサーチでアジアを回ってシャーマンの取材をしており、本作品ではシベリアのシャーマンをモチーフにダンサーが動き、モーション・キャプチャにより VR 内でシャーマンのアバターとシンクロします。残念ながら VR のディスプレイは一台しかなかったので、一回の体験につき15人程度参加できるものの VR 体験は一人しかできません。その VR 版を体験させていただいたのですが、目の前には実際にダンサーが踊っているものの、自分にはデフォルメされたシャーマンとして見える不思議な距離感がありました。
