---
layout: post
title:  "p5.js でモーショングラフィックス"
author: naoto
categories: [ report ]
tags: [ essay, japanese ]
image: assets/images/2019-12-04-p5js-motion-graphics.png
description: "雑感"
featured: true
comments: true
---

p5.js でモーショングラフィックスをします。私はエディタには[公式](https://editor.p5js.org/)のものをよく使っていますが、codepen なら Qiita に埋め込みできるとのことなのでそちらを使うことにします。

# p5.js で基本のスケッチ

まずは四角を動かす最低限のスケッチです。

<p class="codepen" data-height="500" data-theme-id="default" data-default-tab="js,result" data-user="naotohieda" data-slug-hash="VwYZxxe" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="VwYZxxe">
  <span>See the Pen <a href="https://codepen.io/naotohieda/pen/VwYZxxe">
  VwYZxxe</a> by naoto hieda (<a href="https://codepen.io/naotohieda">@naotohieda</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

ポイントは`draw`関数の変数`t`と`tween`です。

```javascript
  let t = millis() * 0.001;
  let tween = t % 1;
  let w = width / 4;
  let x = tween * (width - w);
  let y = tween * (height - w);
```

アニメーションをする都度`millis()`関数を呼んでしまいがちですが、実は同じ`draw`内でも呼ぶタイミングによって`millis()`の結果が少しずつ変わってしまうため、`draw`の初めに`t`に格納するのがおすすめです。`frameCount`なら誤差は出ませんが、フレームレートに依存してしまうため処理落ちの際にアニメーションが遅くなってかっこ悪いのでここでは`t`にこだわることにします。`t`の値は常に大きくなっていくので、周期的なアニメーションをするために`t % 1`で小数点以下だけを残して`tween`に格納しました。

## イージング

上の例では速度が一定で味気ないので、イージングを使うために[こちら](https://gist.github.com/gre/1650294)のコードを拝借します。

```javascript
EasingFunctions = {
  // no easing, no acceleration
  linear: function (t) { return t },
  // accelerating from zero velocity
  easeInQuad: function (t) { return t*t },
  // decelerating to zero velocity
  easeOutQuad: function (t) { return t*(2-t) },
  ...
}
```

例えば先のコードで

```javascript
  let tween = EasingFunctions.easeInOutCubic(t % 1);
```

とすると

<p class="codepen" data-height="500" data-theme-id="default" data-default-tab="js,result" data-user="naotohieda" data-slug-hash="abzoGQo" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="abzoGQo">
  <span>See the Pen <a href="https://codepen.io/naotohieda/pen/abzoGQo">
  abzoGQo</a> by naoto hieda (<a href="https://codepen.io/naotohieda">@naotohieda</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

というように動きに緩急がつきました（可読性のため、`EasingFunctions`は HTML の`script`タグ内で定義しました。ついでに、自前のカラー・スキーム用ライブラリを使って色を付けています。）。

## 周期ごとのトリガー

次に、一周期ごとに何かイベントをトリガーすることを考えます。簡単な例として背景色を変えることにしましょう。一番簡単でバグが少ないのは次の方法です。

```javascript
let lastT = 0;

function draw() {
  let t = millis() * 0.001;
  if (Math.floor(t) - Math.floor(lastT) > 0) {
    // 次の周期に移った
  }
  lastT = t;
  ...
}
```

`floor()`は小数点以下を切り捨てるので、`lastT`（前回の`draw`での時間）から一の位が一つ増えたことがわかります（厳密には処理落ちがひどい場合などに二周期以上飛ばしてしまう可能性もありますが、ここでは無視します）。この`if`文の中で、例えば背景色のインデックスを変えてみましょう。

<p class="codepen" data-height="500" data-theme-id="default" data-default-tab="js,result" data-user="naotohieda" data-slug-hash="yLyBjZG" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="yLyBjZG">
  <span>See the Pen <a href="https://codepen.io/naotohieda/pen/yLyBjZG">
  yLyBjZG</a> by naoto hieda (<a href="https://codepen.io/naotohieda">@naotohieda</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## lerp を使う

同様に四角の動く先を周期ごとに変えてみることにしましょう。イージングを使わないで、

```javascript
  x = lerp(x, x_destination, 0.1);
```

とする方法もありますが、いくらループさせても限りなく近づくだけで`x_destination`と等しくなることはないので、ここでもイージングを使います。面倒ですが、前回の位置を`prevPos`に、目標の位置を`nextPos`に格納します。`draw`の動きに関係ある部分だけ抜き出しました。

```javascript
function draw() {
  let t = millis() * 0.001;
  if (Math.floor(t) - Math.floor(lastT) > 0) {
    prevPos = nextPos;
    nextPos = {x: random(width - w), y: random(height - w)};
  }
  lastT = t;

  let tween = EasingFunctions.easeInOutCubic(t % 1);
  let x = lerp(prevPos.x, nextPos.x, tween);
  let y = lerp(prevPos.y, nextPos.y, tween);
  rect(x, y, w, w);
}
```

これで四角形が一秒ごとに違う地点に向かうようになります。

<p class="codepen" data-height="500" data-theme-id="default" data-default-tab="js,result" data-user="naotohieda" data-slug-hash="YzPKvzZ" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="YzPKvzZ">
  <span>See the Pen <a href="https://codepen.io/naotohieda/pen/YzPKvzZ">
  YzPKvzZ</a> by naoto hieda (<a href="https://codepen.io/naotohieda">@naotohieda</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## シーケンス

上の例では目標位置をランダムにしましたが、シーケンスを用いたのが次の例です。

<p class="codepen" data-height="500" data-theme-id="default" data-default-tab="js,result" data-user="naotohieda" data-slug-hash="MWYgXYd" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="MWYgXYd">
  <span>See the Pen <a href="https://codepen.io/naotohieda/pen/MWYgXYd">
  MWYgXYd</a> by naoto hieda (<a href="https://codepen.io/naotohieda">@naotohieda</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

# 外部ライブラリを使う

Processing には Ani というライブラリがありますが、JavaScript にもイージングをするための便利なライブラリがありますので、ここでは [TweenLite](https://greensock.com/docs/v2/TweenLite/) を紹介します。まず、HTML にライブラリをインクルードします。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/latest/TweenLite.min.js"></script>
```

`pos`オブジェクトの`x`と`y`を一秒間で`2`と`1`にイージングする場合、

```javascript
let pos = {x: 0, y: 0}

TweenLite.to(pos, 1 /* seconds */, {x: 2, y: 1});
```

で済んでしまいます（下のサンプルコードでは`ease`変数を定義してイージングの設定をしました）。内部でタイマーが動いて自動的に`x`と`y`が変化するので、一度`to()`を呼んでしまえばこちらでは何もする必要はありません（もともとは DOM の値をアニメーションするためのライブラリですが、同じように JavaScript のオブジェクトの変数もアニメーションすることができます）。

<p class="codepen" data-height="500" data-theme-id="default" data-default-tab="js,result" data-user="naotohieda" data-slug-hash="YzPKvWQ" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="YzPKvWQ">
  <span>See the Pen <a href="https://codepen.io/naotohieda/pen/YzPKvWQ">
  YzPKvWQ</a> by naoto hieda (<a href="https://codepen.io/naotohieda">@naotohieda</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

だいぶコードがすっきりしました。また、拡張性も高くなったので、最後に四角の数を増やして回転のアニメーションを加えたサンプルで締めたいと思います。

<p class="codepen" data-height="500" data-theme-id="default" data-default-tab="js,result" data-user="naotohieda" data-slug-hash="mdybKwX" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="motiongraphics test with tweenlite adv">
  <span>See the Pen <a href="https://codepen.io/naotohieda/pen/mdybKwX">
  motiongraphics test with tweenlite adv</a> by naoto hieda (<a href="https://codepen.io/naotohieda">@naotohieda</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>
