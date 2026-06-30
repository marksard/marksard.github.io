---
layout: post
title: Passive VCAあれこれ
date: 2026-06-05 20:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20260605_IMG_0121.jpg
---

![img](/assets/photos/20260605_IMG_0121.jpg)

アナログスイッチICを使わず、簡単にミュートできるような回路を探していたときに、JFETでミュートするシンプルな回路を見つけました。

[https://sound-au.com/articles/muting.html](https://sound-au.com/articles/muting.html)

これってパッシブ回路として動くよな、ということで少しアレンジしたのがこちらです。

![img](/assets/photos/passive_lpgvca_02.png)

正電圧でミュートOFFにしたいのでPch JFETに変更し、秋月で見つけた2SJ498を採用しました。

ただし2SJ498はON抵抗がやや高めなので、R4・R5の値を少し上げています。また、FETを2組使っているのは、ゲート0V時の音量をより抑えるためです。最終的にACカップリング構成にしました。

CVへ入力信号より少し大きめの正電圧を与えると歪まず通過し、CV電圧が下がるに従って正電圧側が歪みながら音量も下がっていきます。

少々クセはありますが、パッシブVCAとして機能します（もちろんミュート回路としても）。ただ、EGと組み合わせて遊んでいると、減衰時の歪み方が少々きつく感じることもあります。

---

そういえば以前作って、Xに動画を上げたこちらのパッシブ回路もありました。

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">VCA/RingMod/WaveshaperなPatchPalを作った<br>2N5452じゃなくパーツ箱で見つけたJFETだからQuincas氏のと波形は違うかも <a href="https://t.co/HK0dcK30WC">pic.twitter.com/HK0dcK30WC</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://x.com/marksard/status/1934245367379558730?ref_src=twsrc%5Etfw">June 15, 2025</a></blockquote> <script async src="https://platform.x.com/widgets.js" charset="utf-8"></script>

元ネタはこちら。

<iframe width="560" height="315" src="https://www.youtube.com/embed/QZIKUEhejMQ?si=FAb34Ao3_MZi93vI&amp;start=451" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

波形はかなり変形しますが、CVで音量を制御できるので、これも実質的にはVCAです。

![img](/assets/photos/passive_lpgvca_03.png)

---

## LPGと合体させてみる

閑話休題。

パッシブでVCA的な扱いができるので、定番のLPGと組み合わせたらどうなるだろうと考えました。

LPGは短いパルスを与えてもリリースタイムが長く、用途によっては扱いづらいことがあります。

「もしかして合体させたらいいのでは？」

と思って試してみたのがこちらです。

![img](/assets/photos/passive_lpgvca_01.png)

バクトロールは製品にもよりますが、無通電時は数MΩ程度あります。

そのためJFETを1個追加するだけで十分に音量を落とし切ることができ、追加部品も数点で済みました。

ブレッドボード上で抜き差ししていた部分はスイッチ化。

LPG側のスイッチはON-OFF-ONとして、コンデンサ無しも選択できるようにしました。

また、減衰時に歪む特徴が面白かったので、こちらは **DIRTY VCA** と命名。

LPGとDIRTY VCAを個別にON/OFFできるようにし、組み合わせで出音のキャラクターを選べるようにしました。

---

## デモ

10nFのキャパシタ固定で、FETのON/OFFスイッチは付けたデモ。冒頭はOFFしていて、ONしたらJFETが効いてパツパツ言います。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">今週は効きの良いパッシブVCAを作ってました<a href="https://x.com/hashtag/diyeurorack?src=hash&amp;ref_src=twsrc%5Etfw">#diyeurorack</a> <a href="https://t.co/fzx4k5W9fC">pic.twitter.com/fzx4k5W9fC</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://x.com/marksard/status/2062863861800477122?ref_src=twsrc%5Etfw">June 5, 2026</a></blockquote> <script async src="https://platform.x.com/widgets.js" charset="utf-8"></script>

LPGによる音量変化と、JFETミュート回路による音量変化はカーブが異なります。

そのため、

* 音量変化
* フィルターの掛かり方
* 歪み方

が複雑に組み合わさり、なかなか面白い挙動になります。

ミュート回路単体ではCV低下時の歪みがかなり強く出るのですが、LPGのフィルターも同時に閉じていくため、高域が落ちて歪みも比較的マイルドになります。

ブレッドボード上でフレーズを鳴らして試してみたところ、

JFETありの場合はLPGの長いリリース成分もカットされ、EGカーブに沿ったVCA的な動作になります。

一方で、

* リリースを長くする
* サスティンを上げる

といった設定では、LPGらしい挙動も顔を出してきます。

逆にJFETを外すと歪みがなくなり、LPG特有の長いリリースがよく分かります。


### 波形の変化具合

緑線が三角波出力、青線がCVです。

CVが入力波形のピーク電圧より少し高い場合は歪みませんが、低くなると画像のように波形が歪み始めます。

![img](/assets/photos/20260605_IMG_0118.jpg)

こちらはEGのリリース時。

緑線がオリジナルのサイン波、青線が今回の回路の出力です。

上側の波形が大きく削られるため、サイン波がノコギリ波のような音色へ変化していきます。

![img](/assets/photos/20260604_IMG_0116.jpg)

---

## 基板化

試しに基板へ起こしてみたところなんと2HPに収まりました。

奥行きも最小限なのでブランクパネル代わりに忍ばせておくにも良さそうです。

バクトロール（アナログフォトカプラ）は、秋月で販売されているLCR0202向けのフットプリントを作成しましたが、VTL5CやLCR0203もおそらく実装できそうです。

次回基板発注の際に、一緒に出そうと思います。

![img](/assets/photos/passive_lpgvca_04.png)

---

