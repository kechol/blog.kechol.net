---
layout: post
title: Jekyllでブログを始めるときのTips
date:   2013-11-19 23:19:00
categories: ruby
---

[Jekyll](http://jekyllrb.com/) で何度目かのブログを始めてみた。

Jekyll自体は前に少し試したことがあったのだけど、なんだか2013年の5月にver1.0が出たらしく、だいぶ使いやすくなっていた。(現在はver.1.3.0)
このブログを構築するにあたり、少し調べたことをメモしておく。


## Jekyllの概要

[Jekyllいつやるの？ジキやルの？今でしょ！](http://melborne.github.io/2013/05/20/now-the-time-to-start-jekyll/)

この記事が歴史的経緯も含めて非常に詳しい。

現在のバージョンでは<code>jekyll</code>コマンドを利用してローカルで記事を確認したりすることができる。便利。

記事自体はMarkdownで書ける。ちなみにJekyllではMarkdownのパースをするのに少し前まで[maruku](https://github.com/bhollis/maruku)を使っていたっぽいが、今は[redcarpet](https://github.com/vmg/redcarpet)を使っているようだ。

Jekyllは[octopress](https://github.com/imathis/octopress)と一緒に利用されることが多かったように思うけど、今ではJekyll自体にページングやカテゴリの機能があり、さらにシンタックスハイライトやGistとの連携までできる。詳しくないが、シンプルなもので（かつ柔軟性を求めるのであれば）、octpressは今更必要ないと思う。


## Jekyllの記事リンク

Jekyllで作ったサイトでは、記事へのパスが<code>/:categories/:year/:month/:day/:title.html</code>という形式になっている。
カテゴリが２つ以上あったりすると、階層が違っているようで気持ちが悪い。

このパスは、<code>\_config.yml</code>で

{% highlight yaml %}
permalink: /:year/:month/:day/:title
{% endhighlight %}

の一行を追加すれば変更できる。

permalinkの形式についてはここをみる: [Permalinks | Jekyll Docs](http://jekyllrb.com/docs/permalinks/)



## JekyllでSASSを使う

Jekyllの最初のテンプレートはSASSを使っていないけど、自分でサイトをデザインするなら使いたい。そこで、Jekyllのプラグインを使うという手がある。

プラグインについてのJekyll公式のドキュメントはここ: [Plugins | Jekyll Docs](http://jekyllrb.com/docs/plugins/)

SASSの利用について、少し調べるとまず[noct/jekyll-sass](https://github.com/noct/jekyll-sass)というGemが引っかかってくるのだけど、これはおすすめしない。
それよりも[Sass plugin for Jekyll](https://gist.github.com/wolfeidau/960150)を使う。こっちは30行しかなくてシンプル。

{% gist 960150 %}


このプラグインを使うには、<code>\_posts</code>などと同じ階層に<code>\_plugins</code>というディレクトリを作り、そのディレクトリ配下にこのファイルを置く。*.rbという形式になっていれば勝手にJekyllがプラグインとして扱ってくれる。

このプラグインはSCSS形式での記法に対応しているが、SASS形式でコンパイルしたい場合は10行目と21行目で<code>scss</code>となっている部分を<code>sass</code>に置換すればいい。

{% highlight diff %}
@@ line 10
-      ext =~ /scss/i
+      ext =~ /sass/i
@@ line 21
-        engine = Sass::Engine.new(content, :syntax => :scss, :load_paths => ["./css/"])
+        engine = Sass::Engine.new(content, :syntax => :sass, :load_paths => ["./css/"])
{% endhighlight %}

一つ注意なのは、JekyllでConverter形式のプラグインを使ってビルドするファイルはYAMLのヘッダをつけなければいけないということ。SASSファイルも例外ではない。

公式に

> Remember your YAML front-matter<br>
> Jekyll will only convert files that have a YAML header at the top, even for converters you add using a plugin.

と注意書きがある。（最初なぜコンパイルされないかわからなくてちょっとハマった）


なので、SASSファイルは

{% highlight css %}
---
---

body
  margin: 0
{% endhighlight %}

のように先頭に<code>---</code>を二行追加する。
ただし、<code>@import</code>で呼ばれるSASSは<code>---</code>をつけてしまうとコンパイルエラーになってしまうので注意。


## JekyllのシンタックスハイライトとGist

Jekyllのシンタックスハイライトは[Pygments](http://pygments.org/)互換で行われているので、シンタックスハイライトのスタイルを変更するには、元々あるsyntax.cssの内容を[richleland/pygments-css](https://github.com/richleland/pygments-css)あたりのCSSで置き換えれば良い。

ただ、このスタイルはそれぞれクラス名が違っていたりするので、適宜変更する必要がある。<code>.highlight</code>以下の要素にスタイルが適用されるようにすれば問題ない。

また、JekyllはGistを表示する際、外部のスタイルシートを呼んでくるので、せっかくのシンタックスハイライト用スタイルが上書きされてしまう。（しかも上書き後のスタイルが綺麗に適用されない）

なので、style.cssのそれぞれのプロパティには<code>!important</code>をつけておくと無難。


## JekyllをS3でホスティングする

JekyllはローカルでmarkdownをHTMLにビルドできるので、S3でホスティングができる。

S3へのデプロイは[s3_website](https://github.com/laurilehmijoki/s3_website)というgemを使うのが簡単。もともとJekyll用に作られたものらしい。

<code>s3_website.yml</code>という設定ファイルを作れば、あとはコマンド一発でデプロイできる。

最低限の設定ファイルもこれだけ。あとはオプションでgzipしたりもできる。

{% highlight yaml %}
s3_id: YOUR_AWS_S3_ACCESS_KEY_ID
s3_secret: YOUR_AWS_S3_SECRET_ACCESS_KEY
s3_bucket: your.bucket.com
{% endhighlight %}

このs3\_websiteは、Jekyllがビルドした<code>\_site</code>以下のファイルを基本的にすべてS3にアップロードするので注意が必要。(例えば重要な設定ファイルなどが全世界に公開されてしまう！)

ビルドの際に<code>\_site</code>以下にファイルをコピーしないようにするには、Jekyllの<code>\_config.yml</code>で設定する。

{% highlight yaml %}
exclude: ['README.md', 'Gemfile', 'Gemfile.lock', 's3_website.yml']
{% endhighlight %}

のような形でコピーしたくないファイルを列記していけば、コピーはされない。

