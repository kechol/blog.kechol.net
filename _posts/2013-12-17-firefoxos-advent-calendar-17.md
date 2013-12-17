---
layout: post
title:  FirefoxOSアプリのUIデザイン
date:   2013-12-17 20:53:00
categories: firefox
---

こんにちは、けちょる([@Kechol](https://twitter.com/Kechol))です。このエントリーは[Firefox OS Advent Calendar 2013](http://www.adventar.org/calendars/103) 17日目の記事になります。


デバイスAPIの話とかは他のみなさんが書かれると思ったので、今回はFirefoxOSアプリのUIデザインについて少し書きたいと思います。


## Firefox OS アプリのUIってただのHTMLでしょ

その通りです。だからWebデザイナーさんとか余裕でアプリが書けます。最近はBootstrapとかFoundationとか、CSSのUIフレームワークもたくさんあるのでそういうのを使えば簡単にそれっぽいUIが作成できると思います。

とはいえ、iOSやAndroidにUIガイドラインがあるように、FirefoxにもUIのガイドラインがあります。


## Firefox OS アプリのUIガイドライン

こちらのサイトがFirefox OS のUIガイドラインです。

[Firefox OS Guidelines — Mozilla Style Guide — Mozilla](https://www.mozilla.org/en-US/styleguide/products/firefox-os/)

### アプリ例

上記のガイドラインから画像を借りてきました。標準的なFirefox OS アプリはこんなかんじです。

![メールアプリ設定画面](/img/posts/20131217/example-light-app.png)


例えば、この画像のナビゲーションには戻るボタンがあります。Firefox OS の端末には、Androidと違って戻るボタンがないので、ナビゲーションバー(Firefox OS では単にヘッダと呼ばれます)には戻るボタンが必要になります。

mozilla のブログに Firefox OS のデザイン理念を解説した記事があります。読みましょう。
[MozCamp Warsaw: Design Principles Behind Firefox OS UX | Mozilla UX](https://blog.mozilla.org/ux/2012/09/mozcamp-warsaw-design-principles-behind-firefox-os-ux/)


ちなみにFirefox OS は現在UI/UXの刷新プロジェクトが進行中だそうで、ver1.4,1.5あたりでがらっと見た目が変わるかもしれません。(もっとフラットな感じかな？)


### 単位

Firefox OS のアプリはレスポンシブなUIが求められるため、基本的に`rem` (るーとえむ) という単位でスタイルを記述します。この単位はルート要素(html要素)に対して相対的な値をとるための単位ですが、Firefox OS では特に指定しなければ 1rem = 10px となるようです。


## UIパーツいろいろ


### アイコン

![アイコンルール](/img/posts/20131217/icon-elements.png)

アイコン60px四方に収まるように作る必要があります。形には3種類あります。丸と角丸、それに四角です。それぞれ同じ大きさに見えるように、若干大きさの指定が異なります。また、角丸の角は8pxです。


![firefoxアイコン](/img/posts/20131217/icon-firefox.png)

Firefoxのアイコンのように、正確に円になっていなくてもOKみたいです。


### ヘッダ(ナビゲーション)

![ヘッダルール](/img/posts/20131217/header-elements.png)

標準的なヘッダは 5rem の高さを持ちます。

1. 左側のエリアは、戻るボタンやキャンセルボタン、メニューの表示ボタンなどに使われます。このエリアのボタンは他のエリアよりも暗くなっているのが基本です。

2. タイトルはいつも左寄せです。もし、タイトル左側のエリアにボタンがなくても、タイトルの位置はずれないようにする必要があります。

3. 右側のエリアはなにかアクションするためのボタンを置くエリアです。ボタンはラベルをつけず、アイコンのみのものが推奨です。2つ以上のボタンは縦線で区切りつつ、右寄せにして並べます。


### ボタン

ボタンには大きく、アクションボタンとリストボタンがあります。

![アクションボタン](/img/posts/20131217/button-action.png)

アクションボタンはいわゆるボタンです。文字は中央揃えが基本です。通常は白色、アクションを促したいものは青色になります。アカウントの削除など、破壊的な操作をするものには赤色を利用し、危険性を示します。


![リストボタン](/img/posts/20131217/button-list.png)

リストボタンはiOSのアクションシートに使われるようなボタンです。

1. ボタンの文字は左揃えになります。
2. 端から1.5remの余白が必要です。
3. アイコンを配置する際は3rem四方のものを利用します。
4. 右端の矢印があるボタンは値が選択式のものです。(例えば画像では日付を選択する)


それぞれのボタンの使いどころとしては例えばこんな感じです。

![ボタンの使いどころ](/img/posts/20131217/button-elements.png)

アクションシート的な使い方をする場合、キャンセルボタンはアクションボタンになります。


### スイッチとか

![スイッチとか](/img/posts/20131217/bool-elements.png)

iOSでは標準的でない、チェックボックスやラジオボタンのUIがあります。逆にHTMLでは見かけないスイッチボタンなんかもあります。

ティックと呼ばれる部品は他と使いどころの区別に困りますが、ティックはOffの際に何も表示されないので、ユーザが選択していることを明示したい場合にはより有効化と思います。
また、特にチェックボックスはリストの編集中の選択(削除候補など)に使うためのUIのようです。


<br>

と、こんな感じで様々なUIのルールがあります。さらに詳しくは本家のガイドラインをご覧ください。

[Firefox OS Guidelines — Mozilla Style Guide — Mozilla](https://www.mozilla.org/en-US/styleguide/products/firefox-os/)


## どうやってマークアップするのか

UIのガイドラインがあっても、マークアップがそれぞれ違ったら気持ち悪いですね。
そんな時にお役立ちなのが以下のサイトです。

[Building Firefox OS](http://buildingfirefoxos.com/)

標準UIのマークアップ例や画面遷移のアニメーションなんかの紹介があります。

マークアップ用のCSSはもちろん、それ以外にも、UIをまとめたPSD素材やアイコン、OSのフォントなんかもダウンロードできます。


## おわり

それではこのへんで。長らくお付き合いいただきありがとうございました。

明日は[@noir_73_s](https://twitter.com/noir_73_s)さんです。
