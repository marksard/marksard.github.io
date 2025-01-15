---
layout: post
title: 自作モジュラーシンセ振り返り
date: 2025-01-15 11:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20240414_IMGP8686.jpg
---

## 自作モジュラーシンセ振り返り

なんとなく記憶の片隅に置くかわりにまとめを書いてみようと。  

23年の秋～24年の春ごろにかけてイーストコーストなシンセの基本モジュールを作ってきました(記事化はかなり遅れて出してたので記事日は当てになりません…)  

シンセ作ってみたいなーって思ってた動機としてアナログ回路でひと通り作ってみたかったというのがあるんです。  
3340や2044みたいな専用IC、OTAなんかも使ってちょっと楽しちゃいましたけどアナログ回路で作れて満足しています。（JUNON DCOはマイコン使いましたが）  
モジュラーシンセだとアジャイル開発っぽく機能（モジュール）単位でリリースして積み上げてく感じで目標と区切りが明確にあって取り組みやすいのはいいですね。  

今後はSAW COREのVCOとかステートバリアブルフィルタなんか作ってみたいなと思ったりはしています。  

### VCO (Voltage Controlled Oscillator)

#### EEAOVCO (AS3340)

[github](https://github.com/marksard/EEAOVCO/tree/main)  
[ブログ](https://marksard.github.io/2024/05/10/eeaoVCO/)  

![img](/assets/photos/20240414_IMGP8686.jpg)  


#### JUNONE DCO (RP2040+OPAMP)

[github](https://github.com/marksard/JunoneDCO)  
[ブログ](https://marksard.github.io/2023/12/18/junonedco/)  

![img](/assets/photos/20231214_IMGP8479.jpg)  

### VCF (Voltage Controlled Filter)

#### Minimalist LPF (V2044A)

[github](https://github.com/marksard/MinimalistLPF)  
[ブログ](https://marksard.github.io/2024/03/19/minimalistLPF-VCA/)  

![img](/assets/photos/20231214_IMGP8492.jpg)  

### VCA (Voltage Controlled Amplifier)

#### Minimalist VCA (V13700)
[github](https://github.com/marksard/MinimalistVCA)  
[ブログ](https://marksard.github.io/2024/03/19/minimalistLPF-VCA/)  

![img](/assets/photos/20231214_IMGP8497.jpg)  

### EG (Envelope Generator)

#### Minimalist ADSR (LMC555)

[github](https://github.com/marksard/MinimalistADSR)  
[ブログ](https://marksard.github.io/2024/04/03/minimalistADSR/)  

![img](/assets/photos/20240402_IMGP8681.jpg)  

### LFO (Low Frequency Oscillator)

#### EightWaveformLFO (OPAMP)

[github](https://github.com/marksard/EightWaveformLFO)  
[ブログ](https://marksard.github.io/2023/12/14/eightwaveformlfo/)  

![img](/assets/photos/20231214_IMGP8466.jpg)  

### MIXER

Minimalist MixMulti（非公開）  


## デジタルモジュール

24年の後半は23年の春夏に作ったRP2040でアレコレ作れるデジタルモジュールを作り直して、アプリも移植・アップグレード・新規追加なんかをやってました。  

### Eurorack Playground MkII

[github](https://github.com/marksard/EurorackPlaygroundMkII)  
[ブログ](https://marksard.github.io/2024/07/26/eurorackplaygroundmkii/)  

![img](/assets/photos/20240718_20240718-IMGP8787.jpg)  

アナログで作るのが面倒くさそうな（また高機能化しにくそうな）機能モジュールってのが結構あるし、アプリを気軽に試作して他モジュールを制御して遊びたいため取り組んでいるモジュールです。
CV制御すればいいやということと、幅も取りたくないので操作系がちょっと弱いんだけど、4in 6outとRP2040のパワフルさに甘えてHW機能に依存しないコードでも複数機能を実装して遊べてるので気に入ってます。  
PWM出力にパッシブローパスかましただけの出力で多少振幅が残ってるんですが、V/OCT出してVCOにいれたのをチューナーで確認しても問題ないレベルだし、ADCもリニアリティがないと言われる割にはV/OCTとして使っても案外大丈夫なのでまぁ大丈夫。（個人の感想です）  

アプリはmkIからかなり増えました。上のmkIIの記事の時からも地味に進化させてます。  

* 「ランダムパターンシーケンサー」＋「ユークリッドリズムトリガー」＋「サンプリング＆ホールド＋クオンタイザー」モジュール
* サンプリングドラム（64ステップ内臓パターンシーケンサーor外部トリガー選択式）モジュール
* 「スムースランダムCV」＋「スムースランダムCVで周波数が動くVCLFO」＋「スムースランダムCVで拍子変動するトリガー」モジュール
* クロックディバイダーモジュール
* 3VCOモジュール

複数機能を組み合わせた複合モジュールもいくつか作りました。  
個人的には良い感じのパターンをランダムに作ってくれるシーケンサーアプリと適当な64ステップのパターンで鳴らしてくれるサンプリンドラムアプリが神アプリです（自分でゆーな）  

ROLANDのT-8で少し凝ったリズムパターン作ったりKORGのSEQ-1を自分でゴリゴリ変化させるのも楽しいけど、程よく良い感じにフレーズ鳴られられるだけでも十分満足…  

#### （シーケンサーモジュールの複合具合と、パターンの自動切り替え）  

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">メインシーケンサのクロックをクロックディバイダで8小節単位まで落としそれをフレーズ生成トリガー入力に入れる<br>するとフレーズが8小節ごとに延々と切り替わる<br>ベースVCOとコードVCOのフレーズが同時に切り替わってる<br>時々エモいフレーズになるのが面白い<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/UDaSbW7Vwk">pic.twitter.com/UDaSbW7Vwk</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1872290247826145565?ref_src=twsrc%5Etfw">December 26, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

#### （「スムースランダムCV」＋「スムースランダムCVで動くVCLFO」＋「スムースランダムCVで拍子変動するトリガー」モジュール）  

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">イジイジしている<br>ユークリッドリズムトリガーをランダムmultiplyで1〜3逓倍して遊んでみた<br>こういうトリガーとスムーズランダムCVを3ch+3chで組みたいなーと思ってるんだけどなかなか手が動かない💦 <a href="https://t.co/eBqaEhGXXS">pic.twitter.com/eBqaEhGXXS</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1854839658381033760?ref_src=twsrc%5Etfw">November 8, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## 最近の試作アプリ

### ChordVCO

ずっと使い回しているオシレータのクラスをベースにして、CoarseをルートにV/OCT入力にあわせてコードを構成する周波数を計算で出すという処理を追加して作りました。  
コードはまだナチュラルマイナーだけ。もう少しレパートリー増やすのもありかな。とりあえずルートや7thを1オクターブ下げるとかは設定でつけてみたけど、設定バリエ増やしたり、転回系をCV制御させてみると面白かったりするのかもねと思ったりしてます。  

mkIIのモジュールが余ってないのでmkIモジュールで作ってみたんだけど、これはそのうちmkII用アプリに移植したい。  
あとはクロックディバイダーとスムースランダムCVのモジュールが中途半端な感じがするので合体させるかブラッシュアップしたいかも。  

#### （ChordVCOであそぶ）

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">本日のモジュラータイム<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/Z46x7JRCSw">pic.twitter.com/Z46x7JRCSw</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1871544830599532996?ref_src=twsrc%5Etfw">December 24, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## 試作をそのまま載せる基板

ブレボ基板の配置のままハンダ付けしたら使えるモジュールというのも作りました。  

### EurorackPlaygroundMkIII

[github](https://github.com/marksard/EurorackPlaygroundMkIII)  
[ブログ](https://marksard.github.io/2024/07/31/eurorackplaygroundmkiii/)  

![img](/assets/photos/20240718_20240718-IMGP8865.jpg)  

これでWavefolderVCA+EGモジュールと2VCAモジュールを作りました。  

* EG+VCA+Wavefolderモジュール [ブログ](https://marksard.github.io/2024/08/26/epmkiii-wavefolder/)
* AS2164(SSM2164) Dual Variable Curve VCA [ブログ](https://marksard.github.io/2024/10/07/epmkiii-as2164-vca/)

去年作ってたMinimalistVCAはもう外してしまってこの2つのVCAを常用しています。  

作りたいなーと思うのはまだまだあるし、今後も有効活用したいかも。  

…とざっと振り返ってみましたが…まだ作りたいなと思いつつ、ケースの空きがほとんどなく、さらにケースを置く場所ももうないのが開発の一番のネックだったりします…誰か新しい家を買ってください(笑)  
