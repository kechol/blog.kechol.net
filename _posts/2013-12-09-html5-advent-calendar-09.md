---
layout: post
title:  HTML5でネットワーク状態をうまく扱うためのAPIたち
date:   2013-12-09 22:20:00
categories: html5
---

こんにちは、けちょる([@Kechol](https://twitter.com/Kechol))と申します。このエントリーは[HTML5 Advent Calendar 2013](http://www.adventar.org/calendars/125) 9日目の記事です。


最近TizenやFirefoxOSなんかがますます盛り上がってきてますね。それに伴い、ネットワークの不安定なモバイル環境でも動くWebアプリの重要度が増してきています。

というわけで今日は、HTML5でネットワーク状態をうまく扱うためのAPIをご紹介します。



## Application Cache

まずは定番の Application Cache です。AppCache とも言われたりします。

AppCache は、Webページや画像などをブラウザにキャッシュする仕組みです。キャッシュするファイルはマニフェストによって管理します。


### AppCache の利用方法

HTMLタグにmanifest属性を使ってマニフェストへのパスを指定することで、キャッシュを有効化することができます。


{% highlight html %}
<html manifest="manifest.appcache">
  ...
</html>
{% endhighlight %}


このマニフェストファイルは`text/cache-manifest`というMIMEタイプで提供します。


### マニフェストファイルの構造

一般的なマニフェストファイルは以下の様な構造になっています。マニフェストの中には`CACHE`, `NETWORK`, `FALLBACK`, あと`SETTINGS`の4つのセクションがあります。

{% highlight bash %}
CACHE MANIFEST
# rev 1

# キャッシュするリソース
CACHE:
index.html
images/logo.png

# 必ずサーバから提供するリソース(キャッシュされない)
NETWORK:
/api
https://api.twitter.com

# オフラインの時、代替として提供するリソース
FALLBACK:
images/ images/offline.jpg
*.html /offline.html
{% endhighlight %}

注意点として、AppCacheによってキャッシュされたリソースは、サーバ側のリソースが更新されても、マニフェストファイルが更新されるまで更新されません。

ですから、マニフェストファイルにはリビジョン番号やハッシュを含めたり、サーバサイドで必ず最新のマニフェストファイルを返すようにする、といった工夫が必要です。


### ツール

マニフェストファイルを生成するツールもいろいろあります。

- [gunta/grunt-manifest](https://github.com/gunta/grunt-manifest) <br>
マニフェストファイルを作ってくれるGruntタスク

- [Manifested](http://manifested.dregsoft.com/) <br>
URLからマニフェストファイルを作るオンラインツール

- [Cache Manifest Validator](http://manifest-validator.com/) <br>
マニフェストファイルのバリデーションツール


### 参考資料

基本的なことしか書かなかったので、`window.applicationCache`でJSから操作する方法や細かな注意点などは参考資料からどうぞ。

- [6.7 Offline Web applications — HTML Standard](http://www.whatwg.org/specs/web-apps/current-work/multipage/offline.html)
- [アプリケーション キャッシュ初心者ガイド - HTML5 Rocks](http://www.html5rocks.com/ja/tutorials/appcache/beginner/)
- [Appcache Facts](http://appcachefacts.info/)
- [Offline Web Applications - Dive Into HTML5](http://diveintohtml5.info/offline.html)


## Online/Offline events

Online/Offline events は、端末がオンラインであるかどうかを取得できるAPIです。

### 現在オンラインかどうかを判別

`navigator.onLine`でbool値を取得できます。

{% highlight javascript %}
if(navigator.onLine) {
    console.log('オンラインです。');
} else {
    console.log('オフラインです。');
}
{% endhighlight %}

&nbsp;

### オンライン/オフラインになった時にcallback

`online`, `offline` イベントを利用することができます。

{% highlight javascript %}
var updateStatus = function(event) {
    console.log(navigator.onLine);
};
window.addEventListener('online', updateStatus);
window.addEventListener('offline', updateStatus);
{% endhighlight %}

注意としては、`navigator.onLine`が`true`であってもインターネットに繋がっているとは限らないということです。ChromeやSafariでは、ローカルネットワークや仮想のネットワークにさえ繋がっていればこの値は`true`になります。

FirefoxやIEでは、ブラウザのオフラインモードをオンにしたときにこの値は`false`になりますが、それ以外の場合は`true`になるようです。(ちゃんと動作確かめてないですが・・)


### 参考資料

- [NavigatorOnLine.onLine - Web API interfaces | MDN](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorOnLine.onLine)
- [Online and offline events | MDN](https://developer.mozilla.org/en/docs/Online_and_offline_events)
- [Offline Web Applications](http://www.w3.org/TR/offline-webapps/)
- [Working Off the Grid with HTML5 Offline - HTML5 Rocks](http://www.html5rocks.com/en/mobile/workingoffthegrid/)


## Network Information API

Network Information API は Device APIs の1つとして策定されているAPIです。端末のネットワークの状態を取得できます。


### ネットワークの状態を取得

`navigator.connection`オブジェクトを利用します。`bandwidth`プロパティでMB/s単位での通信速度を、`metered`プロパティではインターネットへの接続が確立されているかどうかを取得できます。(しかし正直`metered`が厳密にどういう場合に`true`なのかよくわかってない)

{% highlight javascript %}
// オフラインは0, 不明な場合はInfinity, それ以外はMB/s単位での通信速度
var bandwidth = navigator.connection.bandwidth;
var content = '';
if(bandwidth > 2) {
    content = 'http://example.com/high-quality.mp4';
} else {
    content = 'http://example.com/low-quality.mp4';
}
{% endhighlight %}

このように`bandwidth`プロパティはユーザに適切なコンテンツを配信する際に便利です。

{% highlight javascript %}
if(navigator.connection.metered) { // online
    send_something();
}
{% endhighlight %}

&nbsp;


### ネットワーク状態の変化を監視

ネットワーク状態の変化を監視するには、`connection`オブジェクトの`change`イベントを利用します。

{% highlight javascript %}
var conn = navigator.connection;
var updateConnection = function() {
    console.log(conn.bandwidth);
};
conn.addEventListener('change', updateConnection);
{% endhighlight %}

&nbsp;


### 参考資料

- [Device APIs Working Group - W3C](http://www.w3.org/2009/dap/)
- [The Network Information API](http://www.w3.org/TR/netinfo-api/)
- [Network Information API - WebAPI | MDN](https://developer.mozilla.org/en-US/docs/WebAPI/Network_Information)


&nbsp;


今回は、`Application Cache`, `Online/Offline events`, `Network Information API`の3つをご紹介しました。最後までお読みいただきありがとうございました。

明日は[SakamakiM](https://twitter.com/sakamakiM)さんです。どんな記事か楽しみです。
