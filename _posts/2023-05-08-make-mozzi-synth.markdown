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

### 5Vが正確でない

アナログ入力の上限値が4.8Vくらいなので、一応それっぽくテーブルの値を近似値にしたつもりなんですが、オクターブ離れただけでちょっとズレている気がする…この辺は検証の余地あり。  
互換promicroってrawに5V突っ込んでも大丈夫だっけ…？  

### MIDI INがなんか動かない

ブレボで動作してた定数がうごかん…  USBMIDI動くから良いかとちょっと放置…

### アナログピンとデジタルピンの組み合わせでロータリーエンコーダーを使うと反応が鈍い挙動になるタイミングがある

Mozziを使うとアナログに割り当てられているピンの読み込み方法が変わる（関数コールで直接読みに行かずキャッシュを読みに行く、んだったかな）ので、デジタルピンとアナログピンを組み合わせてロータリーエンコーダーで使うと反応が鈍くなる場合があります。  
ブレボで組んでいる時点でなんとなく鈍いエンコーダーがあるなぁと思ってたんだけど、ブレボで接触悪いんだと思い込んでたんですが、どうもこのピンの組み合わせが悪いっぽいです。  
それと、mozziを使うと、既存のボタンやエンコーダーのライブラリでは読めなくなるので適当に自作しました。  

### SSD1306のノイズがダメ

ノイズが酷い。  
LCフィルタでごまかそうと思ったものの、音声波形をめいっぱい出力してもヘッドホンで聴くとちょっとノイズが聞える…  
あと配線を自動にしたせい、というのもある💦  
対策をちょっと変えないとダメっぽ。SSDのGNDはマイクロインダクタ通して分離したほうがいいのかな…  

### promicroのRAMがすくねぇ

少ないのでDelayFeedbackをコーラスに使うくらいしか出来なかった…  
Reverbもきびしい…  
なのでやるならRP2040とかESP32とかがいいのかな？  

## 参考

[Mozzi: sound synthesis library for Arduino](https://sensorium.github.io/Mozzi/)
