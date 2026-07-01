---
layout: post
title: ReverbIsland MkII（Spin Semi FV-1）
date: 2026-07-01 22:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20260531_20260531-IMGP9158.jpg
---

![img](/assets/photos/20260531_20260531-IMGP9158.jpg)  

モジュラーシンセの自作第一弾として作った
[FV-1エフェクターモジュール]({% post_url 2023-12-05-reverbislandmki %})、不満はあれどズルズル使ってたんですが……改善点をまとめて作り直しました。  

## ハードウェア仕様

- **サイズ**：8HP
- **電源**：±12V
- **MCU**：Waveshare RP2040 Zero または互換ボード
- **DSP**：Spin Semi FV-1
- **入出力**
  - L, R入力
    - 基本はモノラル入力（プリセット開発次第でR入力も活用可）
    - L入力はポット付
    - R入力は未接続時Lchをノーマライズ接続
  - P0～2
    - プリセットのパラメーター操作用
  - P0～2 CV
    - パラメータ操作用CV入力（調整ポット付き）
  - L, R出力
- **操作系**
  - バンク、プリセット選択ボタン
- **表示系**
  - バンク、プリセット表示LED

## 回路図

[githubにアプリも置いてます](https://github.com/marksard/ReverbIslandMkII)  

RP2040はEEPROMのスレーブとして動くように作ってi2CでFV-1とつながっています。  
またUSBマスストレージクラスとFATFSのライブラリを取り込んだので、  

PCからドラッグアンドドロップでファイルを渡す  
↓  
取り込んだFV-1のプリセットファイル（IntelHEX）を読んでメモリに展開する  
↓  
FV-1はi2C経由でメモリ上のプリセットを読み込む

という流れを作ったのでHEXファイルを気軽にアップロードできます。  

その関係で、FV-1のスタートアップでEEPROMを読みに行くときにRP2040側のEEPROMスレーブ機能の準備が完了している必要があり、FV-1用の電源を作って電源ICのENでスタートアップのタイミングをコントロールしています。  

あとはレイアウトの問題で配線が厳しそうだったので、LEDを3to8デコーダーICで光らせるようにしたのがいつもとちょっと違う所です。  

FV-1へ送るS0～2を分岐して3to8デコーダーへ（論理が違うチップを選定して）繋げれば出来そうな気がしたんですが、ピンがギリギリ足りたので未検証。  

RP2040はEEPROMエミュとLED制御とスイッチ制御だけなので、EEPROMとバイナリカウンタ（アップダウンカウンタとか）で置き換えれば回路はもっと簡単になるかも。  

![img](/assets/photos/rimkII_schematic01.png)  
![img](/assets/photos/rimkII_schematic02.png)  
![img](/assets/photos/rimkII_schematic03.png)  

## 完成

しばらく基板作ってなかったし、FV-1直実装だし、初めて使うICあるしでドキドキしたけど完全勝利でホッとしました。  

前作のモジュールは秋月の基板付きのFV-1を使ってて、クリップしたときに光るLEDをパネルから見えるようするために変な空間があるレイアウトになってたんですけど、今回は直付けにしたのでノブとジャックをギッチリ詰めました。  

![img](/assets/photos/20260531_20260531-IMGP9158.jpg)  
（そんなに機能はないはずなんですがキチキチです）

![img](/assets/photos/20260531_20260531-IMGP9159.jpg)  
（深さは30mm程度）

![img](/assets/photos/20260531_20260531-IMGP9160.jpg)  
（写真で見るとfv-1のはんだ付け汚いすねすみません）

![img](/assets/photos/20260525_IMG_0077.jpg)  
（左の前作と。4HP分ダイエットしました）

## 自作プリセット

SpinCAD Designerで8プリセット分がんばって自作。  

- リバーブ ×2
- ディレイ + リバーブ
- ステレオディレイ
- ダブリング
- ディストーション＋ディレイ
- コーラス
- リングモジュレーション＋フェイザー

PCでエミュレートさせたときの雰囲気と実機で結構違いがあるのでなかなかムズイです。雰囲気の良いシマーリバーブを自作したいんですけどさらにムズイ。  

## デモとか

パラメータを全部CVで動かせるようにしたんですが、実はまだ生かせてなくて…いくつか使っているだけのを載せておきます。

まずはFV-1のROM-Reverb_1ほぼそのままのリバーブがかかったもの。  
<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">エフェクターは早速使ってるけど問題なし。ファームはもうちょっとイジらないとだけど。<br><br>ミキサーのステレオ入力も増やしたのでCVパンアプリを作ってVCAと組み合わせたらオートパンなんかも実現できて割と満足した <a href="https://t.co/kRdJ4HNCfo">pic.twitter.com/kRdJ4HNCfo</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://x.com/marksard/status/2058902228791136736?ref_src=twsrc%5Etfw">May 25, 2026</a></blockquote> <script async src="https://platform.x.com/widgets.js" charset="utf-8"></script>


こっちはモジュールそのものが写ってませんが……ReverbのFeedbackのパラメータをLFOモジュールから動かしてます。  
<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">TriggerHappy用に新しいアプリを作りました！<br>CV0-5Vをクロックごとに6bit値に変換してそのビットをゲート/トリガーとして出力します。<br>また、同じCVを別のクロックでホールド、VOCTでスケール出力します。<br>HEXBITRと名付けました。<a href="https://x.com/hashtag/diyeurorack?src=hash&amp;ref_src=twsrc%5Etfw">#diyeurorack</a> <a href="https://t.co/hK6IlmlKco">pic.twitter.com/hK6IlmlKco</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://x.com/marksard/status/2061064931630604776?ref_src=twsrc%5Etfw">May 31, 2026</a></blockquote> <script async src="https://platform.x.com/widgets.js" charset="utf-8"></script>


これは自作のステレオディレイ。ディレイが左右で鳴るだけですけどディレイタイムを変えるだけでステレオ感が出てよいのです。  
<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">独特のリズム刻んでる<a href="https://x.com/hashtag/diyeurorack?src=hash&amp;ref_src=twsrc%5Etfw">#diyeurorack</a><a href="https://x.com/hashtag/selfplayingpatches?src=hash&amp;ref_src=twsrc%5Etfw">#selfplayingpatches</a> <a href="https://t.co/jwXQl9vM1k">pic.twitter.com/jwXQl9vM1k</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://x.com/marksard/status/2070142906292437378?ref_src=twsrc%5Etfw">June 25, 2026</a></blockquote> <script async src="https://platform.x.com/widgets.js" charset="utf-8"></script>

## まとめ

OLED表示がなくなってしまったせいで、プリセットの中身とパラメータの割り振りを覚えておかないとまったくわからないのがデメリット…なんかメモでも貼り付けとかないとダメですね。  

アプリはHEXファイルを1ファイルしか読まないので2ファイルを切り替えて読めるようにと思ってたんですが（BANK識別用LEDを用意したんですが）ちゃんと動いたので満足しちゃって放置気味。  

## 現在のラック

いよいよRACK BRUTE 6Uの176HPも使いたいヤツだけで埋めつくされてきました。  

実はRACK BRUTEの3U版も入手済みなんですが、6Uを置いている今の場所には置けないので、自部屋の大規模な模様替えをしないとなとなっています……

![img](/assets/photos/20260531_20260531-IMGP9149.jpg)  
