---
layout: post
title:  AWS上でFirefox OSをビルドする
date:   2014-01-16 00:32:00
categories: firefox
---

ZTE Open(inari)に載っているFirefox OSをアップデートする必要があったのだけど、手元のMac(Marvericks)でB2Gのスクリプトが動かなかったので、AWS上のUbuntu12.04でビルドしてみました。

## 手順

1. EC2を立ち上げる
2. セットアップスクリプトを走らせる
3. バックアップをrsyncする
4. ビルドする
5. できたイメージをローカルにコピーする
6. 端末に焼く


今回はビルドのみAWS上で行い、端末に焼くのはローカルで行います。最新のOSバージョンが`v1.3`だったので、そのつもりです。


ビルドに必要なものとかは基本的には公式のガイドを見れば問題ありません。

[Firefox OS build overview - Mozilla | MDN](https://developer.mozilla.org/en-US/Firefox_OS/Building_and_installing_Firefox_OS/Firefox_OS_build_overview)


### EC2立ち上げ

普通に AWS Consoleから Launch Instance します。 `Virginia`とかのリージョンで立てるとライブラリ群のcloneとか速かったりするかもしれません。

AMIは Ubuntu Server 12.04.3 LTS の64bit(`ami-a73264ce`) を使います。(公式で推奨されています)

ほぼデフォルト設定で問題ありませんが、ハードディスク容量は30GBを指定しておかないとビルド中に容量が足りなくなって死にます。



### AWSのセットアップ

[mozilla-b2g/B2G](https://github.com/mozilla-b2g/B2G) を`git clone`してきたり必要なパッケージをインストールしたりします。

以下のスクリプトを走らせれば一発です。

{% gist Kechol/8294070 %}



B2Gを`git clone`してきたら、ビルドの準備のためのスクリプトを走らせます。

{% highlight bash %}
aws:ubuntu $ cd B2G
aws:ubuntu $ BRANCH=v1.3 ./config.sh
{% endhighlight %}

この`config.sh`は`gonk`や`gecko`など、OSのソースをひたすら`git clone`してくるものです。ソースが大量にあるのでとても時間がかかります。SSHを繋いでおけないと思うので、`tmux`で detach などするとよいです。


### ローカル環境のセットアップ

ビルドしたイメージを焼くのに必要なので、ローカル環境にもB2Gのリポジトリ`git clone`し、さらにAndroid SDKをインストールしておきます。(`adb`が使えればOK)


### バックアップのコピー

無事に config.sh が終わると

> Run |./build.sh| to start building

というメッセージが表示されているはずです。

ビルドを始めたいところですが、`build.sh`は初回のみデバイスからバックアップを作成しようとしてしまうので、ローカルでバックアップを作成して`rsync`しておきます。

バックアップのファイルは`git clone`したB2Gディレクトリの直下に`backup-<device名>`の形で作ります。

デバイスをUSBでつないだ状態で以下のようにします。今回はZTE Open(inari)の想定です。

{% highlight bash %}
local:user $ cd B2G
local:user $ mkdir backup-inari
local:user $ adb pull /system backup-inari/system
local:user $ rsync -av backup-inari <aws host>:B2G
{% endhighlight %}

これでAWS上にバックアップがある状態になりました。


### ビルド

そしてビルドします。長くても半日で終わると思います。

{% highlight bash %}
aws:ubuntu $ cd B2G
aws:ubuntu $ BRANCH=v1.3 ./build.sh
{% endhighlight %}

これも時間がかかるので`tmux detach`推奨です。

もし Build failed! してしまったらググって解決しましょう。

うまくいくと

> echo Run |./flash.sh| to flash all partitions of your device

と表示されると思います。


### 端末に焼く

ビルドした結果は`B2G/out/target/product/<デバイス名>`のなかにあります。*.img ファイルがそうです。

ですが、inari の場合は `boot.img` が作成されません。そこで以下のページからダウンロードしてきます。

[Experiencias con Software Libre: Adapted boot image for use with B2G custom builds on zte open](http://sl.edujose.org/2013/10/adapted-boot-image-for-use-with-b2g.html)

こんな個人サイトで大丈夫かと思うわけですが[公式からもリンクがある](https://developer.mozilla.org/en-US/Firefox_OS/Developer_phone_guide/ZTE_OPEN)ので大丈夫ですたぶん。


ともあれ、ビルドした結果をローカルにコピーしてきます。

{% highlight bash %}
local:user $ cd B2G
local:user $ rsync -av <aws host>:B2G/out .
{% endhighlight %}

ダウンロードした`boot.img`は`B2G/out/target/product/<デバイス名>`のディレクトリに配置してください。


あとはデバイスを繋いだ状態で`flash.sh`を走らせれば完了です。

{% highlight bash %}
local:user $ ./flash.sh
{% endhighlight %}

<br>

お疲れ様でした。



## 補足

- 今回インスタンスのサイズは `m1:medium` で、全部で半日かかりました。お値段 $3 くらい。
- ローカルの Mac上にVagrant立ててビルドしてみる試みは失敗しました。(パッチ当ててもファイルシステムのエラーが直らなかった。。)
