---
layout: post
title: JUNONE DCO
date: 2023-12-18 18:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20231214_IMGP8479.jpg
---

![img](/assets/photos/20231214_IMGP8479.jpg)  

## デジタル・アナログのハイブリッドなオシレーター

前回作ったLFOの回路をまだブレボで試してた頃、SYNC入力にシーケンサーで再生した矩形波を入れて鳴らして遊んでたんですが、  
「このままV/OCT回路どこかに入れてVCOにならないん？」  
と思って調べてたらJUNOのDCO回路の解説を見つけました。  

[The Design of the Roland Juno oscillators](https://blog.thea.codes/the-design-of-the-juno-dco/)

ざっくりと要約すると、
* 積分回路のキャパシタの電荷をトランジスタをスイッチして抜く
* トランジスタはクロックパルスで鳴らしたい任意の周波数でON動作させる
* 周波数によってキャパシタの電荷の抜け具合が変わり振幅も変わる
* それを補正するためにオペアンプに振幅制御用の電圧を入れ振幅を一定にする

というもので「クロックパルス」と「振幅制御用の電圧入力」をarduinoマイコンで制御させたら簡単なのでは？と思って作ることにしました。  

* 仕様
    * 6HP
    * ±12V(実測各13mA) +5V(実測24mA)
    * 入出力
	  	* 約10Vppバイポーラ ノコギリ波、矩形波、ミックス波の3出力
		* V/OCT入力（～5V）
	 	* PWM入力（矩形波、ミックス波に対応、バイポーラ入力）
 		* CV入力（～5V）
    * 操作系
		* Frequency
		* Fine
		* PWM Depth
		* CV Depth

回路図とソースコードをgithubに置いています。  

[JUNONE DCO](https://github.com/marksard/JunoneDCO)  

![img](https://raw.githubusercontent.com/marksard/JunoneDCO/main/_data/JUNONE%20DCO%20Schematic%20rev1.0.0.png)  

基本的な部分は解説に倣ってますが、RP2040を使う部分や機能追加してたりもするので各部調整しています。  
あとノコギリ波形はリニアじゃなく気持ち対数変化するよう、帰還部にキャパシタと並行に抵抗を追加したり。  
周波数が絡むCVはRP2040に入力し、V/OCT変換などを通してクロックと振幅制御電圧を出すようにしています。  
振幅制御電圧の範囲とV/OCTの補正はアプリ内部の固定値で微調整する感じ。  

RP2040の周波数制御と振幅制御出力につけるオペアンプが2回路、発振コアに1回路、ノコギリ波と矩形波の出力に2回路。TL074とTL072を1つずつ使って1回路余るので、このあまったオペアンプでノコギリ波と矩形波をミックスした波形も出すようにしました。  

ブレボで試作してそれを回路図に起こしたときにLFOより部品点数が少ないからこれなら手実装でも良いかな？って思ったので、LFOのIOパネルとユニバーサル基板を早速使ってみることにしました。ポットやジャックも綺麗に使いきれそうなのも動機ですね。  

![img](/assets/photos/20231214_IMGP8479.jpg)  
（雑に切ったマステに機能名書いて貼り付けるだけで済ませました。これくらいのざっくり感もよき。ちなみに名前はJUNO ONE DCO→JUNONE DCO）  

![img](/assets/photos/20231214_IMGP8489.jpg)  
（RP2040 ZEROの下にも部品を実装した関係でZEROの部分が出っ張ってます）  

![img](/assets/photos/20231214_IMGP8484.jpg)  
（ユニバーサル基板へ実装時の配線がしやすいような部品の配置にしてくのが結構手間で途中で諦めた(笑)…2.54mmピッチだと最大10穴で、DIP8とか14とかで幅4穴取られるとサイドに3穴づつしか残らないから結構厳しい…）  

![img](/assets/photos/20231214_IMGP8485.jpg)  
（RP2040ZERO横置きでギリ6HPでした。基板作る場合でもリード部品とZERO載せタイプで作れそう）  

## 波形

波形もgithubに載せてますが、ノコギリ波形は超低域はパルスっぽく、低域は丸みを帯びたカタチ、高域になるにつれてシャンとしたノコギリ波形になります。この辺は制御アプリの振幅補正の調整次第で低域からシャンとしたノコギリ波になるんですが、この低音のサウンドはサイン波みたいなどっしりした低音にノコギリ波の倍音成分がある、みたいな好みなサウンドになってるのでこれで調整終えてます。  

![img](https://raw.githubusercontent.com/marksard/JunoneDCO/main/_data/junondco_wave_saw.png)  
（500Hzの波形。100Hzあたりからおおむねこんな感じ）  
![img](https://raw.githubusercontent.com/marksard/JunoneDCO/main/_data/junondco_wave_saw_low.png)  
（32Hzの波形。ダイオードで整流したみたいな波形だけど、ちょっと303な雰囲気がある）  

ミックス波形も好きなサウンドで、コアが生成した上り方向のノコギリ波形と矩形波を反転増幅でミックスして出力しています。なので波形は下がり方向になっています。で、何もしないと矩形波に近い出音なんだけど、PWMで揺らしてやるとノコギリ波にPWMが効いてるような波形になって、コーラスがかったようなリッチな出音になります。  

![img](https://raw.githubusercontent.com/marksard/JunoneDCO/main/_data/junondco_wave_mix.png)  
![img](https://raw.githubusercontent.com/marksard/JunoneDCO/main/_data/junondco_wave_mix_pwm.png)  
（PWMで両端近くまで揺らしたところ）

矩形波に関してはいたって普通。パルス幅の指定ポットは省略。個人的にパルス幅を偏らせて音作った記憶がないので…（大体LFOとかで動かしてる）  

## 使用感など

デジタル的に周波数を制御してアナログ波形を生成しているので、繊細なV/OCT回路を必要としないのがイイですね。電源入れてすぐでも安定しています。

ただしクロック周波数あたりからの高調波が乗ってくる感じなのが気になるんですよね。対策でアプリ側は高速動作させて高調波をなるべく追いやってますが、それでも2kHzあたりから目立ってくるので気になるといえば気になる…あと仕方ないけどFM入力が0-5Vに制約されてることくらいか…でもそれくらい。  

## デモなど

LFO、DCOとこの次に作ったLPGを組み合わせたデモっぽい動画です。  
接続は、

* 一番左のシーケンサー
    * V/OCT→DCOのV/OCTへ
    * GATE→LPGのCVへ
    * MIDI OUT→Roland T-8へ
* LFO
    * 三角波→DCOのPWMへ
    * LFOサイン波→0～5V変換→DCOのCV(FM)へ
* DCO
    * 矩形波→LPG入力へ
* LPG
    * OUT→エフェクターのINへ

とつながってて、DCOの音をLFOで激しく揺らしたり、LPGのエンベロープとフィルターを操作して音に変化をつけて遊んでいます。  
音程がLFOからFMしてたり手でもグリグリやっててグッチャグチャだけど、こういう操作ってソフトシンセでは直観的に楽しめないからこそ（頑張れば出来ることもあるけど）楽しいんですよね。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">自作モジュラーシンセ<br>最近完成したDCO,LFO,LPGの組み合わせで最小のモノシンセを構成できました<br>やっとneutronの力借りずに単体で遊べる💪<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/fSA3HTfNOH">pic.twitter.com/fSA3HTfNOH</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1719661214513705019?ref_src=twsrc%5Etfw">November 1, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


こちらはシーケンサーの動画ですが、DCOのSAWをneutronのVCF,VCAを通して出しています。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">新しいステップシーケンサーが動き出したぜ<br>ランダムに作ったシーケンスの再生方向をキーとゲートが独立して動くデモ<br>ゲートは休符、ショート、ハーフ、ロング、あとタイもあるので303風味を永遠に楽しめる<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/WxelTKMiDG">pic.twitter.com/WxelTKMiDG</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1721734634357289273?ref_src=twsrc%5Etfw">November 7, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


実はVCO専用チップのcool audio V3340や、±12Vでも使えるNOTゲートシュミットトリガーICのCD40106BEを買ってあるので、そのうちこれらのVCOも作ってみたいし、このDCOも基板に起こして専用パネル作りたいなとか、2VCO化したいなとか、やりたい事は溜まる一方です💦  
