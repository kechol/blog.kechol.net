---
layout: post
title:  Firefox OS の開発用端末ZTE Openが届いたのでレビューする
date:   2013-12-21 13:33:00
categories: firefox
---


Firefox OS の開発用端末である ZTE Open が手に入ったのでレビューしたいと思います。

![Firefox OS 届いた](/img/posts/20131221/IMG_0332.jpg)


## これはなに？

Mozilla 公式の Firefox OS やそのアプリの開発用端末です。Mozilla が ZTE とパートナーシップを組んで作っているものだそう。

Firefox OS が載る端末はは安価(で非力)なものが多いので、実機で確認しないといけないんですが、Firefox OS が最初から入っている端末は日本では発売されていません。

この端末は国内から購入ができるため、日本の開発者にとってはありがたい代物です。


## どこで買えるの？

eBayで買えます。

[ZTE Open Powered by Firefox OS 3G Unlocked Smartphone Orange eBay Exclusive | eBay](http://www.ebay.com/itm/331031989534)

香港から発送されます。僕は住所間違えてて３週間届かなかったのですが、通常は１週間程度で届くみたいです。

## 開けてみた


### 内容物

![内容物](/img/posts/20131221/IMG_0336.jpg)

説明書、マイク付きイヤフォン、USB-microUSBケーブル、USB変換アダプタ、ZTE Open本体、あと保証書なんかが入ってます。

![開発者向けメッセージ](/img/posts/20131221/IMG_0373.jpg)

説明書の裏には開発者向けのメッセージが。大したことは書いてありません。



### 本体

![前面](/img/posts/20131221/IMG_0337.jpg)

![厚さ](/img/posts/20131221/IMG_0338.jpg)

iPhone4との比較です。大きさがほぼ同じ。ZTE Openのほうが厚みがあります。
重さは120g、スクリーンサイズは3.5インチです。

全面にはホームボタンがひとつだけあります。挙動もiPhoneと一緒な感じみたいです。


### 背面

![背面](/img/posts/20131221/IMG_0341.jpg)

2メガピクセルのカメラがついています。ライトはありません。

ビデオを撮るときは352 x 288サイズのものがとれるのだそう、ちいさい。。


### 内部

![内部](/img/posts/20131221/IMG_0343.jpg)

中にはSIMとmicro SDカードを入れる端子があります。docomoのSIMがささるっぽいです。バッテリは1,200mAh。

ちなみに ZTE Open は技適に通っていないので、日本国内でネットワーク機能を使うと法的に問題があるとかないとか。


## 起動してみた


### 起動中

![起動中](/img/posts/20131221/IMG_0344.jpg)

起動中の画面。イケメンな狐さんのしっぽが泳ぎます。解像度は480 x 320。


### 設定

![設定-言語](/img/posts/20131221/IMG_0348.jpg)

言語設定。ただ、英語しかありません。

あと時刻とかWifiの設定項目とかがありました。WifiはSSIDが隠れていると設定できないので注意してください。(何度も言いますがこの端末は技適には通っていません)

他の画面：

- [設定-Wifi](/img/posts/20131221/IMG_0349.jpg)
- [設定-時刻](/img/posts/20131221/IMG_0351.jpg)
- [設定-コンタクト](/img/posts/20131221/IMG_0352.jpg)
- [設定-データ収集のお願い](/img/posts/20131221/IMG_0353.jpg)

コンタクトの設定はFacebookアカウントからのインポートが設定できるっぽくて未来を感じた。


### チュートリアル

![チュートリアル-スタート](/img/posts/20131221/IMG_0356.jpg)

ホーム画面の簡単なチュートリアルが最初にありました。

他の画面：

- [チュートリアル-スワイプ](/img/posts/20131221/IMG_0357.jpg)
- [チュートリアル-スワイプ2](/img/posts/20131221/IMG_0358.jpg)
- [チュートリアル-アプリの移動、削除](/img/posts/20131221/IMG_0359.jpg)
- [チュートリアル-通知の表示](/img/posts/20131221/IMG_0360.jpg)
- [チュートリアル-ホームボタン](/img/posts/20131221/IMG_0361.jpg)
- [チュートリアル-終わり](/img/posts/20131221/IMG_0362.jpg)

起動中のアプリの表示はホームボタン2回タップじゃなくて長押しなんですね。


### ホーム画面

![ロックスクリーン](/img/posts/20131221/IMG_0364.jpg)

![ホーム画面](/img/posts/20131221/IMG_0365.jpg)

起動しました。ロックスクリーンからはカメラが起動できます。



## 設定画面

![デバイス情報1](/img/posts/20131221/IMG_0367.jpg)
![デバイス情報2](/img/posts/20131221/IMG_0368.jpg)
![デバイス情報3](/img/posts/20131221/IMG_0369.jpg)

出荷時の状態では Firefox OS の ver1.0.1 が入っています。

Git のコミット情報まで入っているあたりが開発者用の端末っぽいです。



<br />


と、とりあえず起動まででした。

Firefox OS を ver.1.2.0 以上にしないと App Manager でアプリを転送できないので、まずは OS をビルドして入れなおすことからしたいと思います。ではでは。


## 参考資料

- [The Firefox OS ZTE OPEN - Mozilla | MDN](https://developer.mozilla.org/en-US/Firefox_OS/Developer_phone_guide/ZTE_OPEN)

- [ZTE Open review: Firefox OS gets off to a modest but promising start](http://www.engadget.com/2013/10/03/zte-open-review/)

- [ZTE Open 日本から買えるようになっています - Google グループ](https://groups.google.com/forum/#!topic/firefoxos/P-L3lo0GDs8)
