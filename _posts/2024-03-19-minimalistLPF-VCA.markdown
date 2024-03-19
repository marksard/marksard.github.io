---
layout: post
title: Minimalist LPF Minimalist VCA
date: 2024-03-19 22:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20231214_IMGP8492.jpg
---

![img](/assets/photos/20231214_IMGP8492.jpg)  

VCFとVCAはやっぱり欲しいということで  

## Minimalist LPF

VCFはモジュラーシンセを作ろうと思い初めた時に秋月で見つけて買っておいたcool audio V2044Aというローパスフィルター用のICを使います。-24dB/oct(4pole)のラダータイプのLPFを少ない部品で簡単に組めるのがすごい。  
互換元のICはかつてのKORG polysixやPPG waveなんかでも採用されていたそうな。データシートと以下のページを超参考にしました。  

* [https://mediadl.musictribe.com/download/documents/coolaudio/docs/Datasheet_CA_V2044A.pdf](https://mediadl.musictribe.com/download/documents/coolaudio/docs/Datasheet_CA_V2044A.pdf)
* [http://forum.anafrog.com/phpBB/viewtopic.php?t=8701](http://forum.anafrog.com/phpBB/viewtopic.php?t=8701)
* [https://electricdruid.net/ssm2044-lp-filter-designs/](https://electricdruid.net/ssm2044-lp-filter-designs/)
* [https://note.com/solder_state/n/n0fabdc23fe1e](https://note.com/solder_state/n/n0fabdc23fe1e)

回路図をgithubに置いています。  
[https://github.com/marksard/MinimalistLPF](https://github.com/marksard/MinimalistLPF)  
![img](https://raw.githubusercontent.com/marksard/MinimalistLPF/main/_data/minimalist_lpf_rev1.0.0.png)  

V2044Aと2回路オペアンプ1個とシンプルになるよう調整。minimalist LPFという名前をつけました。  

フィルター回りの抵抗値やキャパシタはデータシートや作例記事をみながら、発振具合や周波数帯域のポット調整領域をブレボで組んで試聴して使いやすそうな範囲で決めました。  

入力は5～10Vppの音を入れる前提にして抵抗分圧でV2044の入力範囲に入るように調整。OUTは電流出力のためオペアンプでIV変換を兼ねた反転アンプ回路を入れる関係で、入力をSIGIN -側にいれて入力波形の逆相で出ないようにしています。  

Resonanceの回路部はデータシートだとGNDに落としてて、polysixやwaveなんかはGNDに落とさず2-3番を繋ぐような回路になってて、聞き比べてみると  

* GNDに落としたほうはレゾナンスを下げた時と上げた時の音量の差が大きい。下げた時は中低音が強調されるような感じ。そのためかレゾナンスを上げると痩せた音に聞こえる。
* GNDに落とさないほうはレゾナンス下げ切っても少しレゾナンスが効いている風に聞こえる。ただしレゾナンス変化に対しての音痩せはあまり感じない。

という感じで違いが面白いので切り替えられるようにしてみました。C1A/B～C4A/Bの容量によっても変わってくるかもです。  

## 仕様

* 4HP
* ±12V(実測各9mA)使用(10pin)
* 入出力
    * CV入力 x2
    * 音声入力
    * 音声出力
* 操作系
    * FREQ
    * RESONANCE
    * CV1 Freq mod depth
    * CV2 Freq mod depth

レゾナンスCVも入れたかったところなんですが、前述のスイッチ式にした回路もあり場所を確保出来ず断念。次に基板作るなら入れたいところ。  

## 実機写真、使用感など

![img](/assets/photos/20231214_IMGP8492.jpg)  

4HPサイズはポットにカバーを付けられて、ジャックは横にギリギリ2個ぶん並べられました。EG+LFOは入れたいでしょってことでFREQにかかるアッテネータ付きのCVを2つに。  

![img](/assets/photos/20231214_IMGP8494.jpg)  

サイドはこんな感じ。4HPでリード部品だとシンプルな回路でもちょっと厳しめ。  

そういえばDoepferのリファレンスデザインだと電源コネクタは下に配置するのが推奨なので（そのほうが数珠繋ぎのリボンケーブルで繋ぎやすいから？）次回からはその辺も考慮したいな。  

-24dB/octのラダーフィルターってこってり濃厚なめらかって感じの音に聴こえます。効きがよくて高域が抜けにくいからでしょうか？フォンダンショコラみたいな感じ。さっぱり系の-12dB/octのフィルターも欲しい…  

※後日JAMMINGさんの2pole state variable filter kitを買って組み立てました。キャラはやっぱりスッキリした音で良いです。そのうち自分でも回路組んでみたい。  


## Minimalist VCA

秋月でAS2164というVCA用のICもV2044Aと一緒に買ってたので使おうと思ったのですが、このICでシンプルに組む限りではリニア入力に対して指数カーブの出力になるようで、指数タイプのエンベロープジェネレータを繋いだときに急峻すぎるカーブになって使いにくいのでは？となって、AS2164を使うのは後回しにしてまずはリニアタイプのVCA回路を組むことにしました。  

リニアVCAはLM13700というトランスコンダクタンスアンプIC（OTA）を使うとシンプルに組めるっぽい…この前V13700買ったぞ試せるな！という事で、これもデータシートの回路例（回路例も色々あって、特にコントロール・ボルテージ・レジスター、ポットの代わりに挟めるのがよさそう）をみつつ、下記のページを参考にしました。  

* [https://www.coolaudio.com/docs/COOLAUDIO_V13700_DATASHEET.pdf](https://www.coolaudio.com/docs/COOLAUDIO_V13700_DATASHEET.pdf)
* [https://www.davidhaillant.com/simple-vca/](https://www.davidhaillant.com/simple-vca/)
* [https://www.analog-monster.de/simple_vca_en.html](https://www.analog-monster.de/simple_vca_en.html)
* [https://benjiaomodular.com/post/2021-12-17-lm13700-vca/](https://benjiaomodular.com/post/2021-12-17-lm13700-vca/)

回路図をgithubに置いています。  
[https://github.com/marksard/MinimalistVCA](https://github.com/marksard/MinimalistVCA)  
![img](https://raw.githubusercontent.com/marksard/MinimalistVCA/main/_data/minimalist_vca_rev1.0.0.png)  

インターフェース基板をLPFと共通化した関係で見づらくなってしまったんですが、electric thingsさんのsimple-vcaをベースにMODしたようなカタチになっています。VCFよりシンプルだし消費電流もめちゃ低いです。  
V13700はトランスコンダクタアンプ(Operational Transconductance Amplifier: OTA)が2回路、ダーリントン接続のトランジスタが2回路。  
OTAの一つはCV入力に割り当ててVI変換してもう片方のOTAのiabcへ、もう片方のOTAの入力はコントロールされる側の電圧を入力。出力にダーリントントランジスタを繋いでIV変換して出力を得る、という流れ。  
CV入力側のiabcはCV入力に対するゲインを決めるのでポットで可変にしました。  
CVの電圧に依存しますが、CV5V程度の入力で、GAINが2時あたりからマイナス側がクリップして歪んでいきます。音作りにも使えるような歪み方をしてたのでそのまま採用しています。  

入出力ともに直流を受け付けるので、CVの電圧を別のCVでコントロールするというのも出来ます。  


## 仕様

* 4HP
* ±12V(実測各3mA)使用(10pin)
* 入出力
    * CV入力 x2
    * 入力
    * 出力
* 操作系
    * OFFSET BIAS（センターで0）
    * GAIN
    * CV1 Amp mod depth
    * CV2 Amp mod depth

## 実機写真、使用感など

![img](/assets/photos/20231214_IMGP8497.jpg)  

VCAはレイアウトが共通で役割が違います（Freq/Reso→Bias/Gain）  
シンプルにまとまって良い感じです。  

BIASを右いっぱいにしてGAINもあげていくとCV入力なくてもサウンドを通すので出音のチェックが出来たり、CVのオフセットの調整も出来ます。  
このVCA回路の特性上CVがマイナス側に触れると出力が閉じられるんで、例えばビブラートさせたい時は一工夫必要そうですね。  
ソースとなるLFOにバイアスオフセットかけて正電圧範囲で動作するようにして、ゲートでON/OFFするようにパッチングしたVCAに入れて、そっから出たLFO波形をVCAのCVに入れる、みたいな（文字だとややこしい）  
ゲートON/OFFを直接いれず、ARエンベロープとかスリューリミッターを挟むとフワーっとLFOが効いてくるーとか無くなってくーみたいな感じとか、アタック一瞬だけモジュレーションかけてエレピっぽく、とか応用範囲はいくらでも。  
ワンパッケージシンセとかだとそんなの最初から付いてるぜ？いやいや本家はこっち、むしろいくらでもモジュレーションかけられるのがモジュラーシンセの面白いところ。  

## デモなど

これはブレボで組んでいたころのデモ。  
LPGをエンベロープジェネレータとしてVCFとVCAのCVに入れてます。  
<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">VCAをV13700一発で作ってみた<br>CV入力周りはもちょっと試すけどこれを4HPに突っ込もうかな<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/DiJSEJyHqL">pic.twitter.com/DiJSEJyHqL</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1730426232783978946?ref_src=twsrc%5Etfw">December 1, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


組みあがった頃のデモ。  
RP2040で作った汎用モジュラーにエンベロープジェネレータのアプリを作って、それをVCFとVCAに入れて使っています。  
<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">VCFとVCAを組み立てて…<br>1VCO(DCO),1LFO,1VCF,1VCA,2EG<br>にステップシーケンサーとマルチエフェクターの全モジュラー自作のセットアップが出来ました〜<br>🎛️🎛️🎛️🎛️🎛️🎛️🎛️<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/TNFGg1yXxT">pic.twitter.com/TNFGg1yXxT</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬3/2キーケット沼人の会 (@marksard) <a href="https://twitter.com/marksard/status/1735277069620461948?ref_src=twsrc%5Etfw">December 14, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>