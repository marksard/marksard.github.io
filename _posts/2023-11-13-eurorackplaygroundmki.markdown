---
layout: post
title: Eurorack Playground MkI
date: 2023-11-13 17:45:00 +0900
tag: [hardware, eurorack, synth]
image: 20231113_IMGP8375.jpg
---

## 遊ぶためのモジュール

モジュラーシンセの世界って様々なモジュールがリリースされていて興味惹かれまくりなんですが、個人的な工作趣味としてDIYモジュラーシンセを「調べて作ってつなげて遊んで並べて悦に浸る」をしばらくやっていこうかなと思ってます。  

ひとまずは今年の春頃に作ったシンセとマルチエフェクターをユーロラックケースに収まるようにモデファイしようと考えました。今回は前者の記事です。  

[Mozziライブラリでシンセサイザーを作る]({% post_url 2023-05-08-make-mozzi-synth %}) ではV/OCTやGATEやSYNC INを付けて、所有しているBehringer TD-3のシーケンサーを使って発音させたり、Teenage EngeneeringのPocket Operator Tonic TO-32と同期演奏したり、MIDIの送受信も出来るようにしてDAWと連携させたりという感じでアプリ側の実装を学んだり、ハードウェア側の回路定数をちょこちょこ変えたりと試して遊ぶみたいな要素も含めていたんですよね。  
ユーロラック化するにあたっても  

* アプリの実装によってモジュールの持つ機能が変わる
* 背面に基板を増築して機能を強化する（SPIのDAC追加したり、バッファを追加したりetc…）

というようなモジュラーユニット内のモジュラー化を意図して設計に盛り込んでみました。  
例えばMIDItoCVあるいはCVtoMIDIとか、VCO、EGやサンプラーなんかを単体機能のアプリで作っても、共通のハードウェア上で（入出力の回路定数を変えたり追加基板を載せることで）動作出来る、みたいな汎用性を持ったモジュールが良いなと。  

というのも似たようなインターフェースのモジュールを作るために毎回同じような回路定数のパーツを組み合わせパネルを設計してというのが自分は億劫だなぁと思ったのと、コンパクトにしようとすると使えるGPIOの数やADC数におのずと制限が出るので、自分的に許容範囲内の最低限のユーザーインターフェースと入出力を備えたコンパクトなモジュールを作って基板にしておけば、完成実機とは別に試験機を作りやすく思いついたことも実現しやすいのかなと。  

## 仕様

* 8HP
* 5V(39.2mA。OLEDの更新頻度にもよると思われる)、+12V(12.2mA)を使用(16pin)、USB駆動も考慮
* 2ポット、2エンコーダー、セレクタ(1回路2接点)による操作
* グラフィカル表示（OLED 128x64）
* MIDI IN, MIDI OUT(MIDI OUTはDC出力と切り替え式)
* V/OCT IN, GATE IN（ADCピンと接続、バッファレスで回路定数変更によりOUTも可）
* RCフィルタ＋OPAMP＋ACカップリング付きGPIO0 PWM AC出力（回路定数変更によりDC出力可）
* RCフィルタ＋OPAMP＋カップリングなしGPIO1 PWM DC出力
* V/OCT IN, GATE IN, AC OUT, DC OUTとGPIOはピンをショートさせることで結合（後述）

勘案した結果こんな感じに落ち着きました。  
LFOやVCOをAC出力したい場合を考慮してAC OUTひとつ、V/OCTやエンベロープなどのCVを出力したい場合を考慮してDC OUTひとつ。  
V/OCT INとGATE INはADCピンと繋がるのでアナログのCV入力が可能（GATEは回路定数変更で対応）  
V/OCT IN, GATE IN, AC OUT, DC OUTとGPIOは基板上では結合させず、通常はピン間をショートして使用します。  
基板を拡張する際にはこのピンを中継してバッファを追加するなり、DAC出力と差し替えるなりして使えるようにしています。  
ユーザーインターフェースはOLED128x64ドットの表示と、2つの可変抵抗、2つのロータリーエンコーダー、1つの双方向レバー（2スイッチ）を付けて、メニュー操作、トグルやインクリメンタルな設定、ダイレクトな操作感にも対応出来るようにと最低限用意した感じです。  

回路図とアプリはgithubに置いています。  
[https://github.com/marksard/EurorackPlayground/tree/main](https://github.com/marksard/EurorackPlayground/tree/main)
そのうちオープンソースハードウェアライセンスでkicadプロジェクトを公開したいなと考えてはいるのですが、フットプリントやシンボルなど整理もしなきゃなのでまだまだ先になりそうです。  

![img](/assets/photos/20231113_IMGP8382.jpg)  
ノブはギリギリ操作しやすいかな？くらいの間隔。ジャックやノブパーツの間には割とぎっしりリード部品が詰まっています…  

![img](/assets/photos/20231113_IMGP8391.jpg)  
![img](/assets/photos/20231113_IMGP8400.jpg)  
写真の電源ピンとソケットは2.54mmピッチで揃えてて、背面に追加のユニバーサル基板などをカットして接続しやすいようにしています。  
今はソケットにリード部品の切った脚をつかって一部をショートして使っています。  

## アプリ

アプリはいまのところふたつ。  
ひとつはpromicroシンセ（Mozziライブラリを使用）で動かしていたものを移植して増改築を繰り返してる動作検証用のアプリ。エフェクターの画面と見た目共通化したりして使い勝手があがっています。
もうひとつはステップシーケンサー単体のアプリ。

ステップシーケンサーは検証用アプリにつけてたランダムシーケンサーの機能を強化して単体化。ノートとゲートを独立してシーケンス出来るようにして、再生方向を変えられるようにしています。ノートとゲートの編集機能、ゲートの長さ調整機能も付けて、ノートとゲートの前後移動も出来ます。鳴らして遊びながら作っているので飽きるまで増改築は進みます（）そのうち動画も撮りたいですね。

このポストは旧基板上（後述）で開発中のもので、まだランダムに変化させることしか出来てない時のものですが…片鱗はうかがえるかと。
<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">新しいステップシーケンサーが動き出したぜ<br>ランダムに作ったシーケンスの再生方向をキーとゲートが独立して動くデモ<br>ゲートは休符、ショート、ハーフ、ロング、あとタイもあるので303風味を永遠に楽しめる<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/WxelTKMiDG">pic.twitter.com/WxelTKMiDG</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1721734634357289273?ref_src=twsrc%5Etfw">November 7, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

こちらも旧基板のものですが、シンセアプリとマルチエフェクターモジュール（これは次回記事にします）と、Roland T-8と同期演奏したもの。
<iframe width="560" height="315" src="https://www.youtube.com/embed/kcK_CpzOioQ?si=qq6R5CPKgHLsHEMI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

こちらも同様で、PO-32とBehringer neutronと併せて演奏しています。
<iframe width="560" height="315" src="https://www.youtube.com/embed/XlQaQ5SSPus?si=bKKvWbiKCLvrC8TV" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

今後はDAC基板を追加して、EGやLFO、VCOも単体で作って遊びたいなと思っています。DACはすでにお試しして基本的な処理は出来てるので、LFOやVCOなんかはすぐ出来そうです。画面を作るのが面倒くさいだけ（）


## 旧基板と試作

最後に試作のご紹介…  
この仕様を検討するにあたっていくつか試作しています。  
ひとつはエフェクタ基板([Mozziライブラリでシンセサイザーを作る]({% post_url 2023-08-14-pocketeffector %}))とpromicroシンセ基板を合体させたもの   
もうひとつはrev1.0.0  

![img](/assets/photos/20230823_IMG_5884.jpg)  
（左：promicro基板　中央：合体改造基板　右：rev1.0.0）  

promicroは非力だし128x32ドットは狭かったので、エフェクタ基板はRP2040と128x64ドットのOLED。これが良かったので、心臓はエフェクタ基板に、コントロールとIO部だけpromicroシンセ基板を使う合体技でしばらく検証していました。
で、これの次にrev1.0.0なんですが、エフェクタ基板の100mm角の余白エリアにいれるため急遽エイヤ！で作ったので考慮が足りなかったり気に食わないところが色々あり、作り直したのが今回の基板です。

![img](/assets/photos/20231109_IMG_6118.jpg)  
（奥：rev1.0.0　手前：rev1.1.0）  

![img](/assets/photos/20230828_IMGP8352.jpg)  
旧基板を作ったころ

![img](/assets/photos/20231113_IMGP8378.jpg)  
今。LFOなども作っていってるので順次記事にしたいなと。

