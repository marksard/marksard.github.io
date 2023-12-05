---
layout: post
title: Reverb Island MkI
date: 2023-12-05 20:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20231203_IMGP8421.jpg
---

![img](/assets/photos/20231203_IMGP8421.jpg)  

## 音出していじりながら作りたいので先にエフェクターを

Spin Semiconductor FV-1をRP2040で制御するマルチエフェクターモジュールを作りました。  
今年作った[Spin Semiconductor FV-1でエフェクターを自作する]({% post_url 2023-08-14-pocketeffector %})のものをEurorack規格に合わてモデファイしています。  

回路や動作の基本的なトコロは前述のブログ記事のものと同じで、アンプ部を12V単電源でも動くようにしたり、DRY/WET回路を見直したり、CV入力を追加したり、定数を見直してみたりという感じ。  
このモジュールは最終的な音の出口として用意したかったのと、先に作っておけばこれから作っていくLFOやVCOの動作確認も楽しいはず、という思惑もあって最初に作りました。  
そして思惑通り常にこのエフェクタを通して音だししています。  

## 仕様

* 12HP
* 5V(90mA)、+12V(8.2mA)使用(16pin)、USB駆動も可能
* FV-1のプリセットセレクター、調整ポット搭載
* 入力ピークインジケータ付
* EEPROM 2個搭載可能（セレクター操作による選択）
* グラフィカル表示（OLED 128x64）
* MONO/STEREO入力切り替え式
* 入出力ボリューム調整
* DRY/WET搭載
* CV入力(0-5V)をプリセット調整ポット3つのどれかに割り当て可

回路図とアプリはgithubに置いています。  
[https://github.com/marksard/ReverbIsland](https://github.com/marksard/ReverbIsland)

![img](https://github.com/marksard/ReverbIsland/blob/main/_data/Reverb%20Island%20MkI%20Schematic%20rev1.1.0.png?raw=true)  

![img](/assets/photos/20231203_IMGP8421.jpg)  
（ツマミはDタイプとローレットタイプが安くてデザインも良い感じのがアリエクにたくさんありました）  

![img](/assets/photos/20231203_IMGP8427.jpg)  
（リード部品かつ基板を二階建てにしなかったので横に広く取らざるを得ず）  

本来ならモジュラーシンセのモノラル出力を受け入れるだけで良いと思うのですが、モジュラー化してもステレオ機材を直結して使いたいなという考えでステレオに対応しています。TRSを差し込むとステレオになるような基板付けのパーツがあればなと思ってたんですが、入手しにくそうだったのでSPDTスイッチでの切り替えです。  

DRY/WET回路は、4つの抵抗の間にポットの端を繋ぎ、ポット中間をGNDに落とす方法。この回路では単電源仕様でバイアスがかかっているのでポットの中間はキャパシタを挟んで交流だけGNDへ流しています。  
DRY信号⇔WET信号のポットでの変化具合を聞き比べながら定数を決めました。ここ抵抗が小さいとFV-1に出力音声がフィードバックするし、大きくするとオペアンプでの増幅量が増えてノイズも一緒に増幅…という感じになるので、実質4.7K～10k程度にとどめておくのがよさそうです。WET側がちょっとだけ大きい値なのは、WET/DRYのポットが中間位置の時のバランス的にこれくらいでイイかな的な感じで。  

トータルとしてのゲインは欲張ってないのですが、ガジェットシンセの小さい音量もある程度増幅出来るようなゲインにしています。出力はTL072とかNE5532で7～8Vppくらい。出力に直列の抵抗を入れずにおいたので、ヘッドホンの直刺し駆動も出来るかと思います（挿抜、電源入り切りでポップノイズは多少なりとも出ますが）  

RP2040のADC4系統のうち3つはポットに使って残りの1つをCVに設定しています。アプリでどのポットに対してCV効かせるのか、ポット値からの変化量(Depth)と変化方向を相対的か絶対的かを設定出来るようにしています。今あるアプリではADCの処理を優先的にやらせてないので音作りに積極的に使うよりかはLFOでゆっくり揺らすくらいの感じで使うことを想定しています。  

あとはまだ検証していないのですが、2系統あるRP2040のI2Cのうち1系統はOLEDに、もう1系統はEEPROMとFV-1にジャンパして繋げられるようにしてます。  
RP2040経由でEEPROMに好きなプリセットを書き込むことが出来るし、FV-1に対してEEPROMエミュレートするようなアプリを書けばEEPROM以上の容量を持たせられるなーとか、暇過ぎて死にそうになったら調べて作りたいなって思っています（）  

音だし手順は入力のピークインジケータが光らない程度に入力ゲインを調整して、欲しい出力レベルに大体調整、そのあとDRY/WET調整、必要であれば出力を微調整、という感じです。  

## 旧基板と試作

[Eurorack Playground MkI]({% post_url 2023-11-13-eurorackplaygroundmki %})と同様1回目の試作機が存在します。実はこのエフェクター基板とEurorack Playgoundは基板をくっつけて発注していて、二つ同時に設計していました。双方1回目の試作で不備やら改善点やらがあって、2回目の試作の作成を終えて記事にしています。  
不備といっても、フットプリントのミスとか、遊びで入れてみた機能を除去したりとか、もうちょっと丁寧に部品配置を考えたり、など細かい修正が主。  

![img](/assets/photos/20230823_IMG_5883.jpg)  
（左：ポータブル版　右：rev1.0.0　なんで試作なのに1.0.0なのかというと、下からを刻むと永遠に1.0.0に出来そうにない気がするため）  

![img](/assets/photos/20231203_IMGP8416.jpg)  
（左：rev1.0.0　右：rev1.1.0）  

![img](/assets/photos/20230901_IMGP8356.jpg)  
（PO-32にポータブル版を繋いでよく使ってました。ちなみにチルトスタンドはダイソー、ラックケース上の木の板はキーボードのリストレスト、コタツの電源コードみたいなのは自作ステレオケーブル）  

基本的なエフェクターの機能は1次試作モジュールでも（大きな不満なく）十分に使えてて、なんなら最初に作ったエフェクターモジュールも改造して不満なく使ってたので（こちらは無理やり改造してて見てくれはアレですが）2次試作の基板は10月には届いてたものの作らずに放置してたという…  

## 動画など

[Eurorack Playground MkI]({% post_url 2023-11-13-eurorackplaygroundmki %})で掲載したものと同じですが…  

これは演奏中あえて頻繁にエフェクターのプリセットを変えてます。ノイズもほぼ聞こえないし、切り替わりも速いし、ディスプレイもあると何を設定しているのかわかって良い（自画自賛）  
<iframe width="560" height="315" src="https://www.youtube.com/embed/kcK_CpzOioQ?si=qq6R5CPKgHLsHEMI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

ドラムはポータブル版を繋げて、モジュラーにはモジュラー用を繋げて。標準プリセットにあるリバーブ1や2でも十分好みな音を出してて、これが部品とPCB込みで1万かからず作れるのはとてもお得。試作で合計3台も作っちゃったし、なんなら基板二階建てにしてもう少し省スペースなやつを作りたいなと思うほど…  
<iframe width="560" height="315" src="https://www.youtube.com/embed/XlQaQ5SSPus?si=bKKvWbiKCLvrC8TV" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
