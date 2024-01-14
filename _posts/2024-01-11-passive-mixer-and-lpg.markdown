---
layout: post
title: パッシブ2ch Mixer and パッシブLPG
date: 2024-01-11 13:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20231214_IMGP8503.jpg
---

![img](/assets/photos/20231214_IMGP8503.jpg)  

## 電源不要のモジュールってのがある

オペアンプを使った電源回路が必要なフィルター回路なんかをアクティブフィルターと呼んだり、電源を使わない抵抗だけで組んだミキサーとかCRだけのフィルターとかをパッシブミキサーとかパッシブフィルターと呼んだりするのは知っていたけど、モジュラーシンセの界隈では他にもパッシブ〇〇なモジュールいくつかあって探すだけでも面白いです。  

入出力のインターフェースが5Vpp～12Vppの交流・直流電圧なので、パッシブ回路を通して電圧が半分以下になっても後段どっかに増幅回路あるから補えるんでしょうね。マルチプル、ミキサー、OR回路、CR回路 etc...。その一部をリンクしておきます。  

* [https://www.doepfer.de/DIY/a100_diy.htm](https://www.doepfer.de/DIY/a100_diy.htm)
* [https://lookmumnocomputer.discourse.group/t/passive-multiples-and-more/3053](https://lookmumnocomputer.discourse.group/t/passive-multiples-and-more/3053)
* [https://www.modwiggler.com/forum/viewtopic.php?t=107006&postdays=0&postorder=asc&start=25&sid=a60588edd8dbbee83000d8a4a4ed2b50](https://www.modwiggler.com/forum/viewtopic.php?t=107006&postdays=0&postorder=asc&start=25&sid=a60588edd8dbbee83000d8a4a4ed2b50)
* [https://modwiggler.com/forum/viewtopic.php?t=168810](https://modwiggler.com/forum/viewtopic.php?t=168810)

トランジスタのスイッチ回路を利用したVCAなんかはシンプルで良いです。CVが弱いときに歪むんですが、ピュアオーディオ路線ではないので聴感上耳障りが悪くなければOKという感じのざっくり感も好きです。  

私が作りたいなと思ったのはパッシブローパスゲート(LPG)。  
前回JUNO風DCOを作って、RP2040で作ったシーケンサーアプリでV/OCTとゲートを出して旋律が出るようになってるので、VCFとVCAとEGを作っていく予定なんですが、全部出来るのはまだ先なのでサクッとVCFとかEGを作れないかな？と探しあてたのがパッシブ回路のLPGでした。  

LPGというのは「バクトロール」というLEDと光センサーを組み合わせた部品で作る回路で、  

* LED側をゲートCV電圧を使って光らせる
* Cds(光センサー)で抵抗値が数十Ω〜数メガΩまで可変する
* CRローパスフィルターで使う抵抗として使うと、ローパスの周波数変化＋音量変化（ゲート）がおきる
* CV入力のON/OFFに対するレスポンスが緩くてAtackとReleaseがほんのりかかったエンベロープになる（Sustain levelはCV電圧）

値固定のローパスフィルターとエンベロープをもった簡単なモジュールになります。  

そのLPGに使う「バクトロール」というCdsセルとLEDを組み合わせたパーツって国内になさそうよなー自作かなーと思ってたら「アナログフォトカプラ」という名称で共立とか秋月でも売ってたのでこれを使いました。(Cdsセル単品とLEDを組み合わせて自作したり、電気美術研究舎さんでも海外の製品なんかで使われてるバクトロールを買えるようです)  

最初にごくごくシンプルなパッシブLPGのサンプルをブレボで作ったのですが、減衰するじゃんこんな簡単な回路なのにぃという謎の感動があります。  

LPGだとジャック3つあれば良いので2Uのパネルに複数みっちり組むのが良さそうではあるんだけど、JUNO風DCOで使ったLFOモジュールのインターフェース基板（ポット4個にジャック6個）をまた使いたいがために一捻りしたパッシブ回路を作りました。  

![img](/assets/photos/passive_mixer_and_lpg.png)  

CV入力回路側はダイオードとキャパシタと可変抵抗を追加して、エンベロープをゆるゆるに出来るようにしてみました(Fluffy)。  
CRフィルターのCに直列に可変抵抗を追加して、フィルターの調節が出来るように(Softly)。  
追加で2chのパッシブミキサー回路を組んでて、2chミキサーの出力をLPGにダイレクトに入るようにするスイッチを追加。  

この回路を先日のLFOモジュールのパネルとインターフェース基板を流用して組み込みました。  

インターフェース基板の元回路のスルーホール、実装基板と接続するコネクタ部のスルーホールを流用して部品を付けたり、線材を付けて空中配線して実装しています。


![img](/assets/photos/20231214_IMGP8503.jpg)  
（DCOのときと一緒で、マステに機能を書きました）  

![img](/assets/photos/20231214_IMGP8504.jpg)  
（ユニバーサル基板使わなかったので薄く仕上がりました）  

## 使用感など

SoftlyはMINで倍音が少し削られて優しくなるくらい、MAXでCを通しにくくして減衰だけするVCA風にになってます。まぁそんなに悪くないかな？  
FluffyはMAXはアタックが気持ちゆるくなる程度ですが減衰量が多い…（電流が流れにくくなるためか）  
Cdsセンサーの反応速度の緩さでReleaseが伸びるエンベロープになります。  

下のオシロの写真、緑の線はIN（バクトロールの抵抗側）に入れた5V直流、青はGate信号（3V程度）で、Gate ON/OFFしたときのエンベロープカーブを可視化してみました。

![img](/assets/photos/20240111_IMG_6482.jpg)  
（Fluffy MINのノブ位置。ゲートOFF後ゆっくり閉じてます）  

![img](/assets/photos/20240111_IMG_6481.jpg)  
（Fluffy MINのノブ位置時間軸を拡大。ゲートONから5msくらいで開ききる）  

![img](/assets/photos/20240111_IMG_6480.jpg)  
（Fluffy MAXのノブ位置。ゲートONから20msくらいで開ききる）  

音声波形をINに入れるもよし、上記のように直流をIN入れてエンベロープジェネレータっぽく使うもよしな感じですね。  
ミキサーのほうはパッシブとわかって使う分には問題ないですね。マルチエフェクターに複数波形を入れたいときに重宝しています。

もっとパッシブ回路を作りたい気持ちもありますけど、その辺はまた追々。  

# デモなど

KORG SQ-1を買ったのでそれで遊んだ動画なのですが、この動画では直流5Vを入れてGATEで開閉、その出力をVCF/VCAに入れてエンベロープジェネレーター的な使い方をしています。前述のLPGのゆるい減衰でもそれなりに機能するのが面白いですね。
<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="zxx" dir="ltr"><a href="https://t.co/sfTOXLyq4a">https://t.co/sfTOXLyq4a</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1746415594650656905?ref_src=twsrc%5Etfw">January 14, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


このモジュールの操作動画。こちらは音源を入力してGATEで開閉。なので原音にLPGのフィルタがかかります。  
<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">自作モジュラーシンセ<br>最近完成したDCO,LFO,LPGの組み合わせで最小のモノシンセを構成できました<br>やっとneutronの力借りずに単体で遊べる💪<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/fSA3HTfNOH">pic.twitter.com/fSA3HTfNOH</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1719661214513705019?ref_src=twsrc%5Etfw">November 1, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
