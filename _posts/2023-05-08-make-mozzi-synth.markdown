---
layout: post
title: Mozziライブラリでシンセサイザーを作る
date: 2023-05-08 12:00:00 +0900
tag: [hardware, mozzi, synth]
image: msone_jam.jpg
---

トランジスタとかオペアンプ回路入門的な書籍をペラペラめくってるとオシレータとかパッシブ/アクティブフィルターの回路が載っててアナログシンセ作ってみたいなーと。  
音出すだけならすぐだけど、楽器として実用しようと考えた時何かしらコントローラが必要になってくるわけで。CVとかMIDIはarduinoならすぐ出来るかな？ということでarduino、MIDI to CV、自作モジュラーシンセとかの方向から調べてたら[ハギヲさんのブログを発見](https://note.com/solder_state/n/n71c67b5aaeca)  

え？Mozziライブラリを使えばarduino単体でオシレータになるの？面白そう！（1bit DAC的な感じらしい）  
調べてみるとMozziライブラリはatmega32u4も対応してていっぱい転がっているpromicroでも遊べる！CV/Gateの回路とソースも載せてくれているしこっちも試せそう！ってことでやってみることにしました。  

ブレボ上にGate InとV/Oct In、音声出力の回路を組んで、BEHRINGER TD-3（TB-303クローン）のGate OutとCV Outを繋いで鳴らせました。  

![img](/assets/photos/20230327_IMG_5333.jpg)  
![img](/assets/photos/20230403_IMG_5366.jpg)  

MozziのAPIリファレンスとサンプルを試しているうちに操作関係と画面まわりも欲しくなったりして、そんな感じで遊んでるとブレボ上に載せたボタンやエンコーダーが操作しにくいのが気になってきたので大体の定数と部品を決めて実験基板を作りました。ユニバーサル基板で組めるレベルなんですが、  
ユニバーサル基板にパーツ載せるため基板加工と実態配線図を考える工数＞基板発注工数  
なもので…（基板発注だと配線をオートルータに任せてしまえる、みたいなところもあり）  

![img](/assets/photos/20230418_IMG_5390.jpg)  

## 実験基板のスペックはこんな

* MCU
  * Promicro 5V16MHz互換品指定（Sparkfun promicro純正で動かしたことないので互換品を推奨）
  * Promicroの向きはツルツル面を上向きに載せるタイプ。基板スルーホールはコンスルーピンに対応
* 電源
  * MCUのmicro USBより5V
* 入出力部
  * Gate In
  * CV In(V/Oct仕様)
  * MIDI In (TRS MIDI規格仕様 Tip-5, Ring-4, Sleeve-2)
  * Sound Out (Mono x2 TRS出力)
* 操作部
  * ロータリーエンコーダー x4
  * ボタン x2
  * SSD1306 128x32 OLED
  * リセットボタン
  * 出力レベル調整用半固定抵抗

100mm角に部品保護プレートまで付けて、中々良い感じにおさまりました。ブレボから生える余計な配線がないぶん触りやすくて実験が捗る～  

## 気の向くままに作ってたソフトシンセは最終的にはこんな感じ

![img](/assets/photos/20230418_IMG_5391.jpg)  

* ソフトシンセ部
  * Mozziライブラリを使用
  * OSC x2 (Saw, Saw2, Square, Sineから選択)
  * LowpassFilter (Cutoff, Resonance)
  * EG x2 (Amp Level, Filter Cutoffにアサイン)
  * LFO x1 (Osc Freq, Filter Cutoff, Chorus Timeにアサイン)
  * Chorus/Franger
  * Overdrive
  * ユーザープリセット保存数 x8（増やせるけど遊ぶぶんには8個もあれば…）

* 対応演奏コントロール
  * CV In, Gate Inによる演奏
  * USB MIDI (micro USBから入力)のノートON/OFFによる演奏
  * 内臓の4小節ランダムノートシーケンサーによる演奏

* ランダムノートシーケンサー部
  * 発音タイミングマップをベースにランダム発音タイミング生成
  * フリジアンスケール内、最大3オクターブ+1内で音程ランダム生成
  * BPM 1-255（演奏中変更可）
  * Step 1-16（演奏中変更可）
  * ランダムシーケンサ使用時、Gate In端子は2PPQのSync Outとして機能（外部対応機器と同期可能）

![img](/assets/photos/20230422_IMG_5392.jpg)  
![img](/assets/photos/20230424_IMG_5412.jpg)  

実験中に単体で適当に音鳴らしたいねと適当に作ったランダムシーケンサーが個人的にはお気に入り。  
リズムが乗れないタイミングで鳴られてもなということで適当にタイミングマップを4つくらい作って、ランダムに選択した上で半分の確率で発音タイミングを足すという感じのもの。変更かけると前回フレーズをランダムに少し残しつつ新しいフレーズになるので繋がりやすい。  

と文章で書いてても分かりにくいので紹介動画を作りました。  

<iframe width="560" height="315" src="https://www.youtube.com/embed/e9ANelA-bpI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>  

ランダムシーケンサーを使ってジャムってみました。  

<iframe width="560" height="315" src="https://www.youtube.com/embed/kK5WntNyObE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>  

ソースコードと回路図は以下に。  

[Micro Module Musical Instrument Platform](https://github.com/marksard/MMMIPlatform/tree/master)

実験回路なので余計な部分もあるから、少し整理して名刺サイズで作るのもアリだなとか考え中。  

## 実験基板のちょっときになるとこ

### <strike>5Vが正確でない</strike>

<strike>アナログ入力の上限値が4.8Vくらいなので、一応それっぽくテーブルの値を近似値にしたつもりなんですが、オクターブ離れただけでちょっとズレている気がする…この辺は検証の余地あり。  
互換promicroってrawに5V突っ込んでも大丈夫だっけ…？</strike>  
私が動作確認に使っていたモニターからのUSBポートだけなぜか5.2Vくらい電圧かかってたのでLDOかダイオードで.4V程度ドロップして4.8Vだったらしい…  
ほかのいくつかのポートに刺すと5.0数V前後で、4.6Vくらい。ポートによって電圧ぶれるよねそれは…  
なので4.65Vくらいで変換テーブルを作成して、それにキャリブレーション値を掛けて補正出来るようにして、その設定も保存可能にした。これなら電圧変わっても演奏前に合わせればOK。    

### <strike>MIDI INがなんか動かない</strike>

<strike>ブレボで動作してた定数がうごかん…  USBMIDI動くから良いかとちょっと放置…</strike>  
<b>5/29追記</b>  

* 自作TRS MIDIケーブルの4,5番逆付け
* 基板LEDフットプリントが逆
* 適当に作ってたmidi受信クラスのbegin呼び忘れ
* オシロ確認時1chと2chのグランドを双方のグランドに付けてたせいで変な回路になってた

TLP785と定数のせいかなとか思ってたけど違うくて自分のポカミスのせい。  
デジタル仕様じゃないので速度が足りないとか推奨されてないらしいのだけど、ノートオフオンとかクロック信号くらいなら一応問題なく受信出来てる。  
赤外LED使用の6N136、TLP559と波形の立ち上がり具合を比較して問題なさそうだったけどちゃんと見てないので、安全を取るなら6N136と電源-コレクタ抵抗は3.3kあたりを選んでおくのがよさそうです。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">出来てなかったmidi受信部出来た<br>・自作TRS MIDIケーブルの4,5番逆付け<br>・基板LEDフットプリントが逆<br>・適当に作ってたmidi受信クラスのbegin呼び忘れ<br>・オシロ確認時1chと2chのグランドを双方のグランドに付けてたせいで変な回路になってた<br>と言う複数のポカをやらかしてた…<a href="https://twitter.com/hashtag/diysynth?src=hash&amp;ref_src=twsrc%5Etfw">#diysynth</a> <a href="https://t.co/4DqqFbE628">pic.twitter.com/4DqqFbE628</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1659542632484454405?ref_src=twsrc%5Etfw">May 19, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

### アナログピンとデジタルピンの組み合わせでロータリーエンコーダーを使うと反応が鈍い挙動になるタイミングがある

Mozziを使うとアナログに割り当てられているピンの読み込み方法が変わる（関数コールで直接読みに行かずキャッシュを読みに行く、んだったかな）ので、デジタルピンとアナログピンを組み合わせてロータリーエンコーダーで使うと反応が鈍くなる場合があります。  
ブレボで組んでいる時点でなんとなく鈍いエンコーダーがあるなぁと思ってたんだけど、ブレボで接触悪いんだと思い込んでたんですが、どうもこのピンの組み合わせが悪いっぽいです。  
それと、mozziを使うと、既存のボタンやエンコーダーのライブラリでは読めなくなるので適当に自作しました。  

### SSD1306のノイズがダメ

ノイズが酷い。  
LCフィルタでごまかそうと思ったものの、音声波形をめいっぱい出力しても<strike>ヘッドホン</strike>高能率なイヤホンで聴くとノイズが聞える…（演奏し始めると埋もれるので気にはならないが）  
あと配線を自動にしたせい、というのもあるのかな💦  
対策をちょっと変えないとダメっぽ。<strike>SSDのGNDはマイクロインダクタ通して分離したほうがいいのかな…  </strike>

<b>5/29追記</b>  
ちなみにRAWに5V入れてもダメだった。  
raspberrypi zero(RP2040)系のpi zero、seed xiao RP2040、waveshare RP2040 zeroで動作させると気にかなり静かになったので、互換promicro側の5V LDOもなんか悪さしているのかもしれない。  

### promicroのRAMがすくねぇ

少ないのでDelayFeedbackをコーラスに使うくらいしか出来なかった…  
Reverbもきびしい…  
なのでやるならRP2040とかESP32とかがいいのかな？  

### 計算が追い付かねぇ<b>5/29追記</b>  
のでプチプチ言う時がある…かなり計算量を減らす努力をしたが、シンセアプリの機能を減らして軽くしないと厳しい。  

## 参考

[Mozzi: sound synthesis library for Arduino](https://sensorium.github.io/Mozzi/)
