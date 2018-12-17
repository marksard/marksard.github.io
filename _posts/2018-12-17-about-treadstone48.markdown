---
layout: post
title: Treadstone48を作ってみた
date: 2018-12-17 10:40:00 +0900
tag: [hardware, keyboard]
image: 
---

　この記事は [「自作キーボード Advent Calendar 2018」その１（３まであるよ！）](https://adventar.org/calendars/2954)の１７日目の記事です。  

# 自キ沼に溺れていた一年

<blockquote class="twitter-tweet" data-lang="ja"><p lang="und" dir="ltr"><a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%82%AD%E3%83%BC%E3%83%9C%E3%83%BC%E3%83%89?src=hash&amp;ref_src=twsrc%5Etfw">#自作キーボード</a> <a href="https://t.co/ZG0u33ZkZB">pic.twitter.com/ZG0u33ZkZB</a></p>&mdash; まーく⌨️ (@marksard) <a href="https://twitter.com/marksard/status/1062177706081538048?ref_src=twsrc%5Etfw">2018年11月13日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

　今年は自作キーボードの沼にどっぷりハマってしまいました。キーボードを買う、キーキャップを買う、キーボードを買う…という再帰から抜けでせないまま自分で基板作りもはじめてしまい…実用的なキーボードとして出来上がったのが[Rhymestone](https://marksard.booth.pm/items/1123535)と[Treadstone48](https://marksard.booth.pm/items/1123627)です。今回はTreadstone48をなぜ作ったかについて書いてみようと思います。  

## 1.25U～2.25Uのキーキャップって、実はカッコいいのでは

　元々無刻印派だった私がHelixを作って刻印派に宗旨替え、続いてcrkbd、JJ40、25キーテンキーパッドに40キー分割型試作キーボードと立て続けに作ってると、キーキャップが全然足りなくなります。そのたびキーキャップを買っていましたが、レジェンド（文字）のある1Uのキーキャップはそれほど選択肢がないこと、デザインやカラーを選ぶとお財布にまったくやさしくないという現実に直面します。妥協してAliなどでANSI104に1Uのノベルティキーキャップ（アイコン・ロゴなどが刻印されている）を追加して買ったりもしましたけど、それでもやっぱり選択肢は少ないです。  
　そんな感じであーでもないこーでもないとキーキャップを探す旅を続けてましたが、ついにGMK LaserのCyberDeck(ANSI104セット)とノベルティセットを召喚しました。もちろん手持ちのHelixやJJ40やcrkbdに付けてそのカッコよさに惚れ惚れしましたが、余った1.25U～2.25Uもそのシャープな造形と色、デザインがカッコよくこれを使わないのはモッタイナイ！と思ったのでちょっと試しにレイアウトを作ってみることにしました。  
　最初は文字キーはOrtholinearのままで、最低限欲しいモデファイアキーを並べて…とすると、こうなりました。  

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">ortholinearで104keyキーキャップを使いたいの図 <a href="https://t.co/cbj60hAuHc">pic.twitter.com/cbj60hAuHc</a></p>&mdash; まーく⌨️ (@marksard) <a href="https://twitter.com/marksard/status/1051682202709180416?ref_src=twsrc%5Etfw">2018年10月15日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

　うん、あまりカッコいいものではない…なにかもうちょっと工夫してキレイにおさめたい…  
　ここでふと以前から少し考えていたことがふと頭をよぎりました。  

## キーボードの押しにくいキー、押しやすいキー

　一般的なキーボードの配列はタイプライターを起源とする、左上起点とすると右に0.25Uとか0.5Uずつずれた配置になっています。Row Staggered（斜交配列）と呼ばれていますが打鍵していると、  

「BとYが打ちにくい、でもTとNは打ちやすい」  

という横にズレているゆえの打鍵感の違いがあります。Ortholinearだとそれが平均化しますがTとNは少し遠くなったように感じます。これも解消するとなるとTとBをFキーに、YとNをJキーにOrtholinearからさらに近づける配列が良いのではないか？という案をふと思ったわけです。  

## あわせてみた

　とりあえず文字キー列を真ん中がX字に開くようにずらして配置して文字キーを並べて、ANSI104のモデファイアキーを並べてみたのがこちら。  

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">指の長さの差分を抑えるために少し曲げて内側に寝かせて手を置くと思うんだけど、そう考えると実はこういうrowstaggerdのが良いんじゃないかと考えた。<br>ANSI104のキーキャップがそのまま使えるようにしたのもトピック <a href="https://t.co/LGPNKx6RxK">pic.twitter.com/LGPNKx6RxK</a></p>&mdash; まーく⌨️ (@marksard) <a href="https://twitter.com/marksard/status/1052130250253987841?ref_src=twsrc%5Etfw">2018年10月16日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

　これをさらにキーキャップのスカルプチャ系のプロファイルのRowも考慮して…  

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">この前出してたやつはキャップのRを考えると良くないのでRを加味してリファイン<br>左上のスペースにpromicro置くイメージ（あくまでイメージ）<br>結構長いしPCBのお値段やいかに。。。 <a href="https://t.co/HWsEenUeME">pic.twitter.com/HWsEenUeME</a></p>&mdash; まーく⌨️ (@marksard) <a href="https://twitter.com/marksard/status/1052808227044216832?ref_src=twsrc%5Etfw">2018年10月18日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

　ところどころ隙間がありますがだいたいキレイに並びました。最低限欲しいキーも揃うしANSIのキーキャップに対応するしいい感じに出来そうというのが見えてきました。  
　左上の隙間はpromicroがギリギリ収まりそう、ということでpromicro置き場にしました。左下は角をカット。右の矢印キーとEnter下の隙間はそのままにしています。  

　この仕様で試作機を検証後、さらにモデファイアの配置パターンにバリエーションを持たせられるようにしました。  

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">この前拡張案書いてたけど、再検討してみると穴がいっぱい空くとプレートの強度が心配になってきたうえに下が透けて意図せぬ場所からLEDの光が漏れるのが美しくないので中止しましたすみません。最終的にはバリエーションはこんな感じに落ち着きそう <a href="https://t.co/7p0tejEYIF">pic.twitter.com/7p0tejEYIF</a></p>&mdash; まーく⌨️ (@marksard) <a href="https://twitter.com/marksard/status/1066716813185040389?ref_src=twsrc%5Etfw">2018年11月25日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

　これがリリース版となります。  

## 仕様

　ざっと仕様を。  

* X Row-Stagegrd 47 or 48keys
* Supports ANSI104 Keycaps
* Supports Cherry MX swithes or compatibe switches.
* Supports Kailh MX keyswitch sockets
* Supports Underglow LEDs
* Supports extension keypad (Rhymestone connected as numpad and dual use)

　トピックは誰も（？）やろうとしないような配列と1Uキーキャップからの脱却です。Taihaoのカラフルでダブルショットでお安いものやFilco、Archisiteなどで販売しているキーキャップなどもモデファイアキーを気兼ねなく使えるのでキーキャップを使える範囲はぐっと広くなります。それでいてOrtholinearに近い使い心地です。  
　Kailh MXソケットに対応しておくと、スイッチを交換したり、lubeしたり、PCBとトッププレートをバラせるのも利点。  
　UnderGrowのみでスイッチ毎のLEDに対応しなかったのは、自分が普段透過型のキーキャップを使わないため、UnderGrowで3mmのアクリルクリアのボトムプレートの淵を光らせているほうがよりカッコいいのではないかという意図からです。  
　あと拡張で4x5の格子配列のRhymestoneを繋いでUSBポートを塞ぐことなくキーパッドとして使えるようにしてみました。  

## 所感

　作るところはビルドガイドとして別途書きますのでそのうちに…  

### デザインについて

　ANSI104のモデファイアがほぼキレイに収まります。  
　※一部の写真は試作機のものもあります。

"GMK CyberDeck（Cherryプロファイル）"
![img](/assets/photos/20181103-PB030002.jpg)

"FILCO Majestouch専用104英語配列・2色成型カスタムキーキャップセット紺/白（SAプロファイル）"
![img](/assets/photos/20181128-PB280003.jpg)

"Taihao 104 Key ABS Double Shot Keycap Set ノーチラスカラー（Cherryプロファイル）"
![img](/assets/photos/20181117-PB170019.jpg)

"KBDFuns MDA BigBone（MDAプロファイル）"
![img](/assets/photos/20181208-APC_0802.jpg)

　ボトムはクリアアクリル3mmです。アクリル端がビシッと光っています。最初の試作機ではLED付近にマスキングテープを貼って導光具合を調整して、リリース版ではレーザー彫刻で一部の光を反射させることで導光しています。  

![img](/assets/photos/20181216-IMG_8036.jpg)

### 機能について

　Rhymestoneについてもそのうち書こうと思いますが、拡張パッドとしてTRRSケーブルを介して接続出来ます。デフォルトで用意したのは普段はテンキーパッド、デフォルトレイヤー切り替えでRhymestoneのQWERTY配列マップになって疑似スプリットキーボードとして使えるようになっています。  
　Treadstone48を二台繋げると物理キー96キーぶん使うことも可能ですが…

![img](/assets/photos/20181216-IMG_8037.jpg)

### 配列について

　私個人は長身で手が大きいと思うので参考にはなりにくいかもしれませんが、文字キーを打つ限りはほぼほぼ手首を机に置きっぱなしで打鍵できます。一体型Ortholinearキーボードで感じた窮屈感もなく、型と肘の位置は普通のTKL（テンキーレス）キーボードと同じ感覚かそれより少しゆったり出来るかなといった具合。  
　強いて言えば通常のANSI104キーと比較してShiftキーの位置が1U左に移動しているので、手の大きい私でもShift+Tの打鍵時は少し遠いかなと感じます。ただこの辺はスペースバーにShift機能を兼務させたり、右Shiftキーを矢印キーあたりで兼務させると良い感じになるのではと思います。  

## まとめ

　試作機を作ってからはメインでこれを使用していますがコンパクトにまとまっていてお気に入りです。机に工具やパーツをひろげて何か作り始めても邪魔になることなく、一体型なので移動しやすくて重宝してます。  
　テンキーパッドを付けると使いやすさがぐっと上がりますし、私の環境だとPCのUSBポートからケーブルを引っ張ってこなくてよいぶん配線がスッキリします。  

## 販売します！

　これを少量ではありますが[近日、準備完了次第BOOTHにて販売いたします！](https://marksard.booth.pm/) もしこの記事を見て気になる方はBOOTHのほうへお越しください。  

## これから

　先日あれだけ買わないわーと思っていたGlabBagを購入してしまい、キーキャップを付けるキーボードが無いというステートに移行します。なのでまだしばらくは沼でおぼれてそうです。  
　PCB設計のほうもGBしていたトラックボールモジュールを活用するキーボードを作りたいし、Biaccoさんのアドカレ記事[モジュラーなキーボードアーキテクチャを求めて](http://biacco42.hatenablog.com/entry/2018/12/13/063814)のModuloアーキテクチャなキーボードの設計もしてみたいし、3Dプリンタを楽しんでみたいしと色々やりたいことがあります。  
　来年KUMOとPlanck rev.6が来るし、susuwatariも来るし…  

　明日はあぷろさんの記事「スプリットスペースバーが最高だと言う事」です！  

　俺たちのENDGAMEへの戦いはこれからだ…！  
