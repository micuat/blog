---
layout: post
title:  "Creative Coding Online Meetup 1"
author: naoto
categories: [ report ]
tags: [ essay, japanese ]
image: assets/images/2020-04-11-creative-coding-online-meetup-1-ja.png
description: "レポート"
featured: true
comments: true
---

4月11日に[吉岡さん](https://twitter.com/Junky_Inc)の企画で Creative Coding Online Meetup の第一回が開催されました。もともと vvvv 日本コミュニティのスピンオフとして始まったのですが、 vvvv の [Takuma Nakata](https://twitter.com/takumanakata) さんや[平野佑樹](https://twitter.com/yhy_jp)さんの他にも、 openFrameworks などを使っている[岩谷成晃](https://twitter.com/nariakiiwatani)さんや PCD Tokyo の運営で p5.js で作品を精力的に制作している [Mao Tada](https://twitter.com/t_mao_p) さんなどいわゆるクリエイティブ・コーディングの様々な分野からの発表がありました。アーカイブはこちらから見られるほか、当日のノートは[こちら](https://docs.google.com/document/d/11SjKNMi5zQhoYBrv5ZIXLA-Y0Uc2nKzsqSQBJR7mV5U/edit?usp=sharing)にあります。

<div class="youtube-container">
<iframe class="youtube-video" width="560" height="315" src="https://www.youtube.com/embed/hszSiuyokFo" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

私は発表にあたって何をテーマにすればいいか Twitter でアンケートをとりました。その結果、 Processing / p5.js を用いた絵作りとアートのコンセプトの話が僅差で1, 2位だったのでそこからかいつまんで発表しました。当日は、スライドを p5.js で制作しておいて発表の際に WebSocket でビューワー側のスライドをめくるという、以前から温めていたアイディアを実装しました。今後テンプレートを作って公開できたらと思います。

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe
    src="https://glitch.com/embed/#!/embed/presentation-sync?path=public/main.js&previewSize=100"
    title="presentation-sync on Glitch"
    allow="geolocation; microphone; camera; midi; vr; encrypted-media"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>

上の埋め込みはビューワー側で操作できませんが、 [https://presentation-sync.glitch.me/?login](https://presentation-sync.glitch.me/?login) にアクセスすると admin モードになり矢印キーでスライドを操作できます。今回のスライドは [Glitch](https://glitch.com/) で開発・ホスティングしています。発表の前半はコンセプトの話で、これまで [36C3]({{ site.baseurl }}/36c3/) などのプレゼンテーションをベースにしました。要旨としては、作品の見せ方にこだわるのではなくアルゴリズムを中心に考えており、必ずしもコードを用いるのではなくダンスや織物といったプロセスの中にアルゴリズムとしての考え方を見出すことが制作のテーマです。後半は再利用できる p5.js のコードの書き方を簡単に紹介しました。例えば

```
clear();
push();
strokeWeight(2);
noFill();
rotateX(t * 0.1);
rotateY(t * 0.1);
stroke(255);
box(side);
pop();
```

というコードがあったときに、頭に `pg.` を付加する癖をつけておくと

```
const pg = this;
pg.clear();
pg.push();
pg.strokeWeight(2);
pg.noFill();
pg.rotateX(t * 0.1);
pg.rotateY(t * 0.1);
pg.stroke(255);
pg.box(side);
pg.pop();
```

`pg` に `this` を代入すればメインのキャンバスに描画でき、 `p5.Graphics` のインスタンスを代入するとフレームバッファに描画して後からレイヤー的な処理ができるようになるため後で移植する作業が楽になります。発表では触れませんでしたが、このような描画のブロックを関数やクラス（JavaScript のクラスの実態は関数ですが）にまとめておくと更に再利用しやすくなります。

発表後には質疑応答があり、いくつかコメント等いただきました。

> ひえださんってドイツ歴がどれくらいで、日本がどれくらいなのでしょうか？あと、それ以外も海外歴があったりするのでしょうか？

幼少期に1年オーストラリアにいた以外は学部まで日本で過ごしています。学部では2か月ほどアメリカのヒューストンに留学しました。それから修士でカナダに移った後フリーランスなどをしながら計6年過ごして、2018年に一度帰国してから韓国で3か月レジデンスをして2019年からドイツに移りました。ちなみに2019年の秋には香港で1か月レジデンスしました。

> アルゴリズミックなものに興味をもたれたキッカケってあるんですか？

自閉症スペクトラムなので自分自身にアルゴリズム的な考え方が密接に結びついていると考えています。その一方では言語化できない曖昧さやイメージや言葉、触覚などを行き来するような共感覚的な表現も自閉症スペクトラムに関連していると思っています。その他、 ADHD や識字障害など発達障害全般についてオープンに話しながらそれぞれの似ている点や違った点を探しながら制作を心がけています。

> 海外のクリエイティブコーディングのコミュニティって日本と比べてどうですか？

一概には言えませんが、多様性を重視してオープンなコミュニティを作ろうという動きが欧米では見られます。岩谷さんの発表にもあったニューヨークの [School for Poetic Computation](https://sfpc.io/) や、ドイツを中心にクリエイティブ・コーディングとダンスをつなげた活動をしている [Choreographic Coding Labs](http://choreographiccoding.org/) からは特に強く影響を受けました。立ち上げから現在も運営に関わっている Processing Community Day Tokyo でもそういった精神でコミュニティをつくっています。

> アルゴリズムを勉強する際に参考にしている本やサイトはありますか？

発表時は回答が思いつかなかったのですが、自分としては学部自体に学んだデジタル信号処理や半導体物性といった学問が今でも表現のベースになっています。特に信号処理に関してはフーリエ変換などオーディオでも役に立つことが多いので勉強して損はないと思います。

Creative Coding Online Meetup は月一回程度での開催を予定されているとのことなので、今後も別の形で携わりながら盛り上げていけたらと思っています。また、 Processing コミュニティでも国内外でオンラインのイベントを企画中ですのでぜひ [PCD Tokyo](https://twitter.com/PCD_Tokyo/) をフォローしていただけたら幸いです。
