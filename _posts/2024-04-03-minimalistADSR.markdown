---
layout: post
title: RP2040エンベロープジェネレーターとMinimalist ADSR
date: 2024-04-03 21:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20240402_IMGP8681.jpg
---

![img](/assets/photos/20240402_IMGP8681.jpg)  

DCO(VCO)、LFO、VCF、VCAと作ってきたので、今回はエンベロープジェネレーター（EG）です。  
これを作ればいわゆる普通のシンセ（東海岸シンセシス）的にの最小最低限の機能が自前で揃います。  

## RP2040エンベロープジェネレーター

デジタル版から作ってみました。  

先日の[minimalistLPF-VCA]({% post_url 2024-03-19-minimalistLPF-VCA %}) の基板発注直後くらいに、RP2040を付けた自作の汎用モジュラー  
[Eurorack Playground MkI]({% post_url 2023-11-13-eurorackplaygroundmki %})  
向けエンベロープジェネレーターアプリを書きました。  

ソースはgithubに置いてます。  
ゲートを受信したら時間とともに出力変化させつつステート遷移させる感じの直球なコードです。  
[https://github.com/marksard/EurorackPlayground/tree/main/app/EnvelopeADSR/src](https://github.com/marksard/EurorackPlayground/tree/main/app/EnvelopeADSR/src)  
指数カーブはjupyterでカーブ具合を確認して実装。  
[https://github.com/marksard/EurorackPlayground/blob/main/app/EnvelopeADSR/src/EnvelopeCurve.ipynb](https://github.com/marksard/EurorackPlayground/blob/main/app/EnvelopeADSR/src/EnvelopeCurve.ipynb)  

OLEDのグラフィック表示は簡易的に直線で表現してます。  
個人的な問題としては現状の自作汎用モジュラーはエンコーダー2つにポット2つしかないのでADSRのパラメータを操作するのがちょっとダルい。  
AR/ADのようなタイプにしたほうが逆に使い勝手が良いのかも。  

## 動画・使用感など

操作はこんな感じです的なデモ。エンベロープ波形は動画内で見られます。

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">RP2040でエンベロープジェネレーターを突貫で作った！<br>はやくVCFとVCA届かないかなー<br>LPGはずしてVCF/VCAが4HP2個入ってピッタリと埋まる予定なんだよ<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/e5bh0lxEv4">pic.twitter.com/e5bh0lxEv4</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬3/2キーケット沼人の会 (@marksard) <a href="https://twitter.com/marksard/status/1733337587816026494?ref_src=twsrc%5Etfw">December 9, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

こちらは操作してないけど使った記念的な。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">VCFとVCAを組み立てて…<br>1VCO(DCO),1LFO,1VCF,1VCA,2EG<br>にステップシーケンサーとマルチエフェクターの全モジュラー自作のセットアップが出来ました〜<br>🎛️🎛️🎛️🎛️🎛️🎛️🎛️<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/TNFGg1yXxT">pic.twitter.com/TNFGg1yXxT</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬3/2キーケット沼人の会 (@marksard) <a href="https://twitter.com/marksard/status/1735277069620461948?ref_src=twsrc%5Etfw">December 14, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

### （ちょっと脱線）EurorackPlaygroundMkIにDAC（MCP4922）を載せた

RP2040はDACがなく基本PWM出力のみで1次パッシブフィルタをカットオフ3kHzあたりに設定して使ってたんですが、オシレータとして使うと高調波が結構出てくるし、V/OCT出力に使うと音程が不安定気味で揺れるし、エンベロープジェネレーターとして使うぶんにもやはりちょっと揺れてる感もあり…ということでDACを載せました。  
元々DAC載せて拡張出来るようにSPI通信ピン用ヘッダと、2040のPWM出力ピンとパッシブフィルター＋オペアンプに入る部分はピンをショートさせて使う仕様にしてて、PWMの代わりに外付けDACをインサート出来るように作ってあったのでそれに合うようにユニバーサル基板で回路を連結。回路はほんとにMCP4922載せただけレベルです。  

![img](/assets/photos/20240113_IMG_6485.jpg)  
これを…
![img](/assets/photos/20231113_IMGP8400_2.jpg)  
赤く囲ったところに電源ピン、SPIピン、フィルタ＋アンプ部入力へのピン（写真ではPWM出力につなぐため線材を差し込んでます）を出してあるので…
![img](/assets/photos/20240113_IMG_6491.jpg)  
こうして完成

これで直流として出せるのでエンベロープジェネレータも気持ちいい波形が出ました。  
ステップシーケンサーアプリの1V/oct出力もより正確に安定するようになりました。  
オシレーターアプリは補正もなにも入れてないのでDSSの高調波成分がまだちょっと厳しめ…。ソフト補正を試してみたい気もするけどそのうち…  

こんな感じでちょっとづつ改造とかアプリ作ってくと、もうちょっと入出力とノブ類が欲しいところです。  
ADC入力ジャックx4、DAC出力ジャックx2、デジタル出力ジャックx2、アナログポットx4、エンコーダーx2、レバーx1（orボタンx2）とLED出力4つくらい…RP2040直実装して外部DACやADCを追加で付けて…とか、STM32だとADC多めでDACもついてるモデルもあるのでその辺で…とか、ちょっと考えたり…  
あと±12VでOPAMP駆動させて出力を上げてカップリング無しでバイポーラ出力にも対応して、入出力全部ちゃんとバッファ入れてあげて…みたいなところも手を入れてかないと使い勝手が悪いところもあるし…  

## Minimalist ADSR

そんな感じでデジタル版を作ったのですが、アナログ回路のEGも当然欲しいので作りました。  
ネットを探してるとオペアンプとダイオードだけで作るAD/ARエンベロープジェネレーターがあってそっちも簡単そうなんですが、ADSRタイプが欲しいのでタイマーICの555を使ったエンベロープジェネレーターにしました。  
タイマーICって電子工作ではおなじみなヤツで、EGの基本形はワンショットタイマーの動作。スレッショルドとリセットとディスチャージとアウトの各ピンをうまいこと繋げて実現しています。  

回路は以下がとても参考になりました。  

[https://www.schmitzbits.de/adsr.html](https://www.schmitzbits.de/adsr.html)  
[https://hackabrute.yusynth.net/index_en.php?&arg=4](https://hackabrute.yusynth.net/index_en.php?&arg=4)  
[https://www.n8synth.co.uk/diy-eurorack/envelope-generator/](https://www.n8synth.co.uk/diy-eurorack/envelope-generator/)  

rene schmitzさんのをベースに定数を調整しましたが、簡潔にしていったら結果的にn8synthさんのMODのようになりました。LED出力を追加、Release後のオフセット電圧を抑制するためにダイオードをBAT43に交換し、サスティンポットを100kが使えるように調整したカタチになります。  
このEG回路をVCF/VCAで独立して使いたいなということで2系統準備しました。  

回路図はgithubに置いています。  
[https://github.com/marksard/MinimalistADSR](https://github.com/marksard/MinimalistADSR)
![img](https://raw.githubusercontent.com/marksard/MinimalistADSR/main/_data/minimalist_adsr_rev1.0.0.png)

幅と奥行きを考慮して回路を基板に落とし込むところで、ポット数的に8HPは欲しいなという感じで、奥行き方向、つまりポットやジャックを付ける基板とメイン基板の二段構成にするか、なんとか一段に収めるか、が悩みどころ。今回はコネクタ、ポット、一部のキャパシタやダイオードはスルーホールの手実装にして、その他は片側に表面実装するようにデザインしてPCBAで発注しました。  
表面実装部品だとジャックやポット背面の空きスペースに良い感じにおさまりました。  

* 表面実装部品の入手は数個～数十個レベルだと実は高い
* 何度かリフローで使ってたホットプレート廃棄しちゃった
* 表面実装で数も多いので手実装がちょっとめんどいレベルになってくる

という理由でPCBA頼んじゃいました。特に一番目、今後揃えてく前提で考えても割高感ありましたね…抵抗、オペアンプ、トランジスタ、キャパシタをお買い得感ある単位価格と数量を見定めてカートに詰めてくと数万円しました…円安のせいでしょうかね…  

## 仕様

* 8HP
* ±12V(実測20mA/8mA)使用(10pin)
* 入出力
    * CV(Gate)1
    * CV(Gate)2
    * OUT1
    * OUT2
* 操作系
    * Attack Time x2
    * Decay Time x2
    * Sustain Level x2
    * Release Time x2

## 実機写真、使用感など

![img](/assets/photos/20240402_IMGP8681.jpg)  
（空間が空いてたので図解を入れました…なぜここが空いてるかというと入出力が少ないのもありますが…）  
![img](/assets/photos/20240402_IMGP8682.jpg)  
（基板を一枚にしたのと…）  
![img](/assets/photos/20240402_IMGP8685.jpg)  
（電源回路周りを下部に置くのを優先したため…）  

背面の写真、すでにリード線の抵抗で修正済みです…実はAttack Timeの初期時間の調整に470オームを入れてたんですが、どうもやっぱりもっさりしているので、100オームを並列させてます。0603の100Ωを買ったら差し替える予定です。  

使い勝手的にはいたって普通のEGな感じ。上下のポットの間隔をもう少し広げたいなとか、EG出力はそれぞれ2出力ずつ欲しかったかもとか、ポット制御している部分をCVでやってみてぇ、みたいな欲のほうがいっぱい出てきますね…  
あと下にも書いてますが、もう少しアタックとディケイタイムを伸ばしたいかも。  
PCBAで5枚頼んだので、あと4枚も半完成品が余ってるんですが…あと1基は自分用に、もう一つは予備に置いとくということで2枚余るんですが、欲しい人いたりするのかな？  


## 動画など

ブレボで組んだ頃の。  
ブレッドボードだとポットがグラグラして使いにくいんだけど、コンパクトなのは代えがたい…  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">NAMMの新製品横目に遊んでる <a href="https://t.co/UImNfQM97l">pic.twitter.com/UImNfQM97l</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1748308496083517929?ref_src=twsrc%5Etfw">January 19, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

出来立ての頃の。  
左EGをVCF、右EGをVCAに割り当てて、VCFのEnv Modは閉じ気味、VCAのほうは全開で。  
操作感としてはちょっとポット上下間が詰まり気味なのでノブはもう少し小さいのがいいかもしれない。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">自作のエンベロープジェネレーターが出来たのでみて<a href="https://twitter.com/hashtag/%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#モジュラーシンセ</a> <a href="https://t.co/4emzCD1v8T">pic.twitter.com/4emzCD1v8T</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1762795985544380527?ref_src=twsrc%5Etfw">February 28, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

VCOのパルス波形をgateに入れて、出す周波数に収まる範囲でエンベロープを調整して、エンベロープの台形の音を聴いてみる実験。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">エンベロープジェネレーターの音を聴いてみよう！<br>青波形がDCO<br>緑波形が素のEG波<br>DCO波形をEGのGATEに入れて、EG波形はLPFとVCA通して(もう一つのEGで出力を)制御してます<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/5BVqbh3D0E">pic.twitter.com/5BVqbh3D0E</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1765005038253203657?ref_src=twsrc%5Etfw">March 5, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

## 出力波形

![img](/assets/photos/minimalist_adsr_curve1.png)  

青がゲート信号、黄色がエンベロープカーブ。  
ゲートONでアタックタイム設定までに最大レベルに上がり、ディケイタイム設定までにサスティンレベルへ下がり、サスティンレベルを維持、ゲートOFFでリリースタイム設定までに最小レベルに下がる、という流れ。  

![img](/assets/photos/minimalist_adsr_curve2.png)  

こちらはアタックタイム、ディケイタイム、リリースタイムを最大にしたときのエンベロープ。サスティンレベルは大体中央。  
アタックが1.3sec程度、ディケイが4.5sec程度、リリースが6sec程度といったところでしょうか。ピークは8V（電源電圧の2/3程度が上限。±12V電源直結でブレボで組んだもので計測しました）  
アタックとディケイの上限をもう少し伸ばしたいなぁという気がしなくもない。10uFから22uFくらいにするとちょうどいいかもしれない。  
