---
layout: post
title: Eurorack Playground MkII
date: 2024-07-26 17:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20240718_20240718-IMGP8787.jpg
---

![img](/assets/photos/20240718_20240718-IMGP8787.jpg)  

## アプリ用のプラットフォームモジュールを作り直し

RP2040 zeroを積んだ8HPサイズのユーロラックモジュール[「Eurorack Playground MkI」]({% post_url 2023-11-13-eurorackplaygroundmki %}) を設計して、その上で動くステップシーケンサー、エンベロープジェネレーター、オシレーター、ユークリッドリズムシーケンサーとソフトウェアを作ってきましたが、ハードウェアに不満が出てきたので作り直しました。  

### MkIIの主な仕様

* 入出力
    * CV入力x2
        * 各ch バイポーラ/ユニポーラ切り替え付
    * V/OCT入力専用x1
        * ユニポーラ固定（0-5V）
    * ゲート入力x1
    * PWM出力x6
        * 各ch バイポーラ/ユニポーラ切り替え付
        * 10Vpp以上も可（±12V駆動）
* 操作系
    * アナログポットx1
    * ロータリーエンコーダーx1
    * ボタンx3（うち一つはロータリーエンコーダーのプッシュ操作）
* 表示系
    * OLED
    * プログラム可能なLEDx2（自照ボタン内のもの）
    * PWM出力確認LEDx6
* サイズ
    * 10HP(基板2段)

### （参考）MkI

* 入出力
    * ADC入力x2（回路のパーツ付け直しで出力も可）
    * PWM出力x2（オペアンプ付）
        * DC出力x1
        * ACカップリング出力x1
        * MAX4-5Vpp程度（5Vor12V単電源駆動）
        * ※拡張基板でSPIの2chDAC出力をGPIOの代わりにオペアンプに入れられる機構を装備
    * MIDI IN/OUT
* 操作系
    * アナログポットx2
    * ロータリーエンコーダーx2
    * レバースイッチx1（SPDTなので2ボタン相当）
* 表示系
    * OLED
* サイズ
    * 8HP(基板1段)

不満点というのが  

* 入出力が弱い
* LFOやオシレーターをそのまま受けられるバイポーラな入力が欲しい
* オシレーターアプリなんかはバイポーラ出力が欲しいし、でもトリガーやゲートはユニポーラ出力がしたいし、もっと多チャンネル化したい
* 操作性がイマイチ悪い
* MIDIもう要らない

という感じなので、RP2040 ZeroとSSD1306を使うこと以外は見直しました。  

### 入出力

入出力はパーツ組み換えでの入出力切り替えをやめて固定にして、ユニポーラ・バイポーラ切り替え機能をつけました。LFOやEGを入力したり出力したいのですべてカップリングコンデンサレスで作っています。ソフトウェアから切り替えることも検討したんですがそれはせず、背面のジャンパピンをショートさせる方式にしました。MIDIは結局使わなかったので削除。  

入力系は複数種。  
V/OCT入力はユニポーラ固定、多回転タイプの半固定抵抗で5V→3.3Vに調整して0-5Vまで対応。この抵抗分圧を調整すれば0-8Vとかもイケるとは思うけど、ADCの解像度的に厳しくなるかも。  
CV入力は2つ。アッテネータとポーラー切り替え付き。  
GATE入力は単純にデジタル入力を割り当て。  

出力は6つすべて2次パッシブフィルターを付けたPWM出力。  
バイアスとゲインを調整して整えればバイポーラ時は10Vpp以上。ユニポーラなら8Vppくらい。ユニポーラ設定にしてPWM最大出力時を5Vに調整すればV/OCT出力も出来るかと。  
パッシブフィルターは2次で8kHzくらいに設定。  
またPWM出力にはLEDを付けてどこが出力されているのかわかるようにしました。出力オペアンプの手前につけてるのでバイポーラ出力時はバイアス値出力のため常に光っちゃいますが、トリガーやゲート、エンベロープ、LFOの時は出力の状態がわかりやすいかなと。  
OUT1～OUT4はHWSPI指定のピンに割り当てたので、3.3V電源とSPI DACを追加したDAC化基板を作れるので、音質重視にしたい場合はここだけ作り直すのも良いかなと。  

### 表示・操作系

表示系はOLEDのほかに2つある自照式ボタンのLEDをプログラム出来るようにしました。このLEDはHWシリアルのTX,RX指定のピンに割り当てたので、LEDで光らせる代わりにMIDI送受信回路を繋げてMIDItoCVとかにも。  

自分でグリグリ動かすよりもCVやトリガーで動かせた方がとなったので操作子類は減らしました。プログラム可能なエンコーダーとポットは1つずつ。エンコーダーはプッシュボタン付きのものを。SPDTの両側モーメンタリスイッチはやめて自照式ボタンを2個。  
使い方的にはポットはリアルタイム向きな操作を割り当てる用、ボタンは画面遷移やメニュー操作に、エンコーダーは設定値の変更に、という想定。  

## 回路など

回路とアプリはgithubで公開しています。  
回路図は基板の1段目の回路、基板の2段目の回路、2段目の入出力回路の4枚。  
アプリの解説は特にしませんがVSCodeでplatformIOの拡張を入れればビルド出来ます。  
Arduino IDEと違って設定に書いておけばライブラリやコアを選択してくれるので便利ですね。  
[https://github.com/marksard/EurorackPlaygroundMkII/tree/main](https://github.com/marksard/EurorackPlaygroundMkII/tree/main)  


## モデリング

![img](/assets/photos/20240726_EPMkIIv4.jpg)  

仕上がり想像図。各層接合部の位置あわせはkicad上での重ね合わせで十分なんですけど、厚み方向で問題はないかとか、勘合をみたり雰囲気確認したり、配線作業とか回路図書いていくモチベをアゲるのに使ったりしています。  

## 実機

![img](/assets/photos/20240718_20240718-IMGP8787.jpg)  

届いた基板を組んだ。  
最後まで検討してたのが自照式ボタンで、そのボタン径が違うのでとりあえず小さいほうの径でパネル面に入れてたのですが、最終的に大きい径のボタンを使うって決めたのにパネル面のデザインそのままで発注…したので仕方なくステップドリルで6mm径くらいに拡大して取り付けてます…基板発注後にボタンのオブジェクト追加したレンダ絵作って眺めて完了にするかーと作業してたときに気づいたというね…  

![img](/assets/photos/20240718_20240718-IMGP8793.jpg)  

OLEDは7mmだったかな？のソケットを使って、OLED側のピン付属のプラを除去した上でピンを少しカットして載せています。これでパネル面との空間11mm前後にツラで収まります。ピンだけだとグラグラなので7mmスペーサーでOLEDと基板を固定。  

MkIの時はプラ軸のエンコーダーとポットを使ってたんですが、今回押し込むタイプの操作になり、基板や操作子とパネル面との剛性確保と、プラ軸は軸がグラついて操作感を損ねてたのでナット締め出来る金属軸のものに変更。人差し指とか中指一本で押さえつけるような操作も出来て快適。  

自照式ボタンは基板付けタイプで基板面から11mm内に構造が収まりつつ、パネル面にボタンが突き出るようなやつっていうのが国内のパーツ屋さんでは見かけなかったのでアリエクで購入。押し心地は重めでカチカチ五月蝿いのが難といえば難かも。  

![img](/assets/photos/20240718_20240718-IMGP8803.jpg)  

背面はこんな。  
MkIの時は厚みを抑えるためOLEDと基板の間に挟みこむようなレイアウトにしてたんですが、ソフトウェアリセット・ブートが効かない時に物理BOOT/RESETボタンを押せなかったのが辛かったので今回は押せるように。  

2段目の基板に立っているピンはバイアス設定ピン。GNDにするかバイアス電圧にするかをジャンパピンの切り替えで設定します。  
RP2040のそばにあるジャンパピンは3V3、GND、TX、RXを出してて、通常はLED1,2にショートしてボタンのLED制御に使います。上にも書きましたがMIDI入出力の基板とアプリを作って拡張出来ます。  
背の低い半固定は各出力のゲイン調整用。背の高い多回転タイプはバイアス電圧調整とV/OCT分圧設定用。  

(参考MkI)  
![img](/assets/photos/20231113_IMGP8382.jpg)  
![img](/assets/photos/20231113_IMGP8391.jpg)  
（MkIはパネル|OLED|RP2040|実装基板の4層構造でした）

## アプリと実機動画

すでに3つほどアプリを作ったので、かるく動画だけでも。  

### 3VCO

MkIのSimpleVCOアプリを移植した上でMkII用に機能追加しました。VCO/VCLFO切替x2、VCLFOx1、ホワイトノイズx1。設定でVCOやLFOのピッチをCVで操作するよう割り当てて調整すれば和音ならしたり独立したVCOとして。また外部のEGやフィルターモジュールを駆使して、3VCOとホワイトノイズを使ってバスドラ、スネア、ハイハットの音を作り込んだりも。  
波形選択をCVでやらせれば、1VCOだけでドラムっぽい音を出せたりも。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">BD,SD,HH,ベースとなる元の音を一台で出してみた<br>BDはVCLFOと、CV2に入れたEGでビッチを変化<br>SDはホワイトノイズとスナッピー音のVCO Bをミックス<br>HHはホワイトノイズをHPF通して<br>ベースはVCO Aで演奏しつつLPF通し、CV1に入れた外部LFOでシェイプ変化させてる<br>ボタンのLEDはCV1,CV2チェック用に表示 <a href="https://t.co/1ouWJuhYSU">pic.twitter.com/1ouWJuhYSU</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1804867036100911210?ref_src=twsrc%5Etfw">June 23, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">レイブなサウンドが出てきたんだが <a href="https://t.co/cIuQZ4g0Gk">pic.twitter.com/cIuQZ4g0Gk</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1805974624188145699?ref_src=twsrc%5Etfw">June 26, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


### クロックディバイダー

MkII用新作。外部からクロックを入力したものを分割出力。ドラム音源のトリガーに使ったり、メロディのEGタイミングにしたり…ソフトウェア実装でそこまで速い処理させているわけではないけど普通のレンジで使う分には問題ないはず。/3や/5みたいな奇数出力が簡単に出るのと、内部でリセットをかけて32小節単位でリズムを繰り返す、とか裏拍を刻ませる、みたいなことも簡単に出来ました。  
マルチプライヤーは内部でオシレーターを持ってクロックで受けた間隔で周波数だしてその2倍3倍の周波数で動かしてトリガーさせれば2連符とか3連符は出来そうかな。やってないけど。  
ドラム音源用の定番パターンのプリセット作ったり、プロバビリティー機能を追加するのも楽しいかもしれない。まだやってないけど。  


### リズムボックス

MkII用新作。外部トリガーによるワンショットの発音で、現時点では4ボイスだけど実装は軽いので6ボイス出せそう。各ボイスはディケイタイムとピッチを設定可能。再生周波数とビットは44.1kHz 11bit。pythonで44.1kHz16bitのwavファイルを11bitのc形式配列に変換するコンバーターを使って音色セットを作る感じにしています。RP2040のUSBストレージ機能を使ってドラッグドロップしたものを内部で使えるようにしても良いかも。  
シーケンサー内臓して64ステップで定番パターン鳴らす機能みたいなのを持たせても楽しいかもしれない。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">EurorackPlaygroundMkIIと命名したRP2040シンセモジュール上で動く<br>クロックディバイダーとウェーブテーブルドラム音源アプリが出来た！<br>オシレーターのパルスを分周してドラム音源のトリガーにして演奏してます<br>音色は定番の909<br>まだ波形追加出来るし選択式にしたいな<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/hXvNEgGGdC">pic.twitter.com/hXvNEgGGdC</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1806947165681635455?ref_src=twsrc%5Etfw">June 29, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


### 16ステップシーケンサー

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">MkIの16ステップシーケンサーをMkIIへざっくり移植<br>メニュー体系はもうちょっと考えないとかなぁ… <a href="https://t.co/SiGf11ayRs">pic.twitter.com/SiGf11ayRs</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1815333644552294465?ref_src=twsrc%5Etfw">July 22, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

MkIを移植。操作子類が少なくなったのでそれに合わせて使いやすくしたうえで、一番多用しているランダム生成でのオクターブ範囲やゲート長さ範囲を変更出来るようにして、使っていない機能を削除したりと手を加えてます。  
操作性も問題なかったのであとでMkIのほうにもバックポーティング。  


### ほか

まだ記事を書いている途中のEPMkIIIも加えたプレイした動画。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">音がたくさん鳴るようになってきたので嬉しい☺️<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/eIIcHQcqto">pic.twitter.com/eIIcHQcqto</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1813908860245332002?ref_src=twsrc%5Etfw">July 18, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

ステップシーケンサをマスタークロックにしてベースシーケンス、ユークリッドリズムシーケンサーは上物シーケンス、クロックディバイダーのトリガーをミキサーで混ぜてリズムボックスを叩いて鳴らしています。  
