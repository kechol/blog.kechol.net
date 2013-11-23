---
layout: post
title:  ALTERATION WALLが面白い体験だった
date:   2013-11-22 23:02:00
categories: product
---

たまたま見つけた[ALTERATION WALL](http://aw.hisession.jp/)というサイトが面白かった。ad:tech 2013で展示されていたものらしい。


{% oembed http://www.youtube.com/watch?v=zplQ_LL4Pxg %}


スマートフォンから画像をアップロードすると、目の前の画面に画像の色と同じインクが飛び散る。流れる音楽がかっこいい。

「アップロードする」とか言っちゃうと普通なんだけど、実際には画像をスワイプしたり振ったりして「送り出す」って感じ。それがリアルタイムに目の前の画面に出てくるから面白い。

<br>

[博報堂アイ・スタジオ](http://www.i-studio.co.jp/)が制作しているみたいだけど、こうしたインタラクティブな仕組みをもった広告は確かに人の興味を引きそう。最近はみんなスマホ持ってるから、こういうものが街中に溢れる世の中がいよいよ来る感じがする。(ということを言うのも今更な感じだけど)

ネイティブアプリになると敷居が一段高くなる感じだけど、Flash + HTML5というのでこれまでの資産も使えて、ユーザにとってもインストールがいらないので広告とは相性良いだろうし。

<br>

で、どうやってるのかと思ったらリアルタイム通信にはSocket.IOを使ってた。インクの表示とかはFlash。

スマホを振ったりして画像を送り出すのには<code>DeviceMotionEvent</code>というのが使われてた。StackOverflowのコードを貼り付けておく。

[cocoa touch - Detect a shake in iOS Safari with Javascript? - Stack Overflow](http://stackoverflow.com/questions/4475219/detect-a-shake-in-ios-safari-with-javascript)

{% highlight js %}
if (typeof window.DeviceMotionEvent != 'undefined') {
    // Shake sensitivity (a lower number is more)
    var sensitivity = 20;

    // Position variables
    var x1 = 0, y1 = 0, z1 = 0, x2 = 0, y2 = 0, z2 = 0;

    // Listen to motion events and update the position
    window.addEventListener('devicemotion', function (e) {
        x1 = e.accelerationIncludingGravity.x;
        y1 = e.accelerationIncludingGravity.y;
        z1 = e.accelerationIncludingGravity.z;
    }, false);

    // Periodically check the position and fire
    // if the change is greater than the sensitivity
    setInterval(function () {
        var change = Math.abs(x1-x2+y1-y2+z1-z2);

        if (change > sensitivity) {
            alert('Shake!');
        }

        // Update new position
        x2 = x1;
        y2 = y1;
        z2 = z1;
    }, 150);
}
{% endhighlight %}


なるほど。

HTML5 Rocksにも<code>DeviceOrientationEvent</code>と一緒に解説したチュートリアルがあった。CSS3のアニメーションと組み合わせるのは直感的でわかりやすいかも。

[This End Up: Using Device Orientation and Device Motion As An Input Method - HTML5 Rocks](http://www.html5rocks.com/en/tutorials/device/orientation/#toc-devicemotion)

<br>

スマートフォンとか小さなデバイスをコントローラと捉える、みたいなのはゲーム界隈でよく見る([jsdo.it HTML5-Games](http://games.jsdo.it/)とか)けど、こうやってデバイスの特性を活かしたものを作れると楽しいな、と思った。
