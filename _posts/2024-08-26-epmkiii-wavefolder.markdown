---
layout: post
title: EG+VCA+Wavefolderモジュール
date: 2024-08-26 11:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20240718_20240718-IMGP8804.jpg
---

![img](/assets/photos/20240718_20240718-IMGP8829.jpg)  

## 早速詰め込んでみる

[EurorackPlaygroundMkIII（以下ブレボ基板モジュール）]({% post_url 2024-07-31-eurorackplaygroundmkiii %}) 
で早速モジュールを作りました。  
Moritz Kleinという方の設計のダブルウェーブフォルダー回路です。シンプルなのに鳴りが気持ちよいのです。  

<iframe width="560" height="315" src="https://www.youtube.com/embed/jbStEUfBWXY?si=qJQj-LrEdicI6Cf7" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>  

紹介されている回路はざっくりいうと最初のアッテネータの調整で一段目の折り返し（フォールド）量を決めて、増幅しなおして二段目のフォールド回路を通してさらに折り返して再度増幅して元レベル程度に戻して出力するという感じ。  
ただしこれだけだとフォールド量の調整がポットのみなので味気ない。私はアッテネータ回路を取り払ってVCAを前段に入れてフォールド量をLFOとかEGで可変させて遊んでいたのでこれをそのまんま採用します。（回路図は後述）  

![img](/assets/photos/20240707_IMG_7311.jpg)  

ブレボ上で遊ぶ＆動作確認を済ませてラックに載せたいな、となったら作業開始。  
実装範囲がコンパクトになるようにブレボ上で整理して組みなおして、再度動作確認を済ませます。  
このとき回路定数の記録と頭の整理のために回路図だけでも書いておくと良いかなと。  

![img](/assets/photos/20240707_IMG_7312.jpg)  

穴位置を間違えないように慎重に移植してはんだ付け。  
ポットやジャックのピン位置は基板背面のシルクに書いてあるので見比べながら線材を使って取り出して、ブレボ基板につけた回路と繋ぎます。  

![img](/assets/photos/20240707_IMG_7313.jpg)  

一旦完成…  
したけど早速改造。  
回路図を清書しててオペアンプ1基とダーリントンバッファが1基余っているので、エンベロープジェネレータを追加しました。1モジュールでEG+VCA+ウェーブフォルダーの機能が付いてお得！みたいな？  

## 最終的な回路図  
![img](/assets/photos/eg_vca_wavefolder_schematic.png)  

VCAは以前作ったMinimalistVCAの回路そのまんまです。  
ウェーブフォルダー回路は入口のポットを省略して、オフセットが出力に出やすい回路のためカップリングして、トランジスタは2SC1815/2SA1015ペアに変更。マッチングも取ってなく折り返し量のバランスが悪いので、微調整出来るようエミッタ抵抗を半固定に。半固定抵抗の定数は手持ちのありあわせなので、あるなら固定+微調整のための半固定とかにするのがベターと思います。  

エンベロープジェネレータは入力の電圧を反映するタイプなので増幅も出来るように。  
ゲート信号と、ゲート信号をパルスに変換する回路とをスイッチで切り替えられるように。  
ゲート入力そのままだとASR、パルス変換回路を通すと（Attackは使い物にならないので全閉にして）Decayのみの調整になります。  

* ENVELOPW OUT → CV1、  
* VCA OUT → WAVEFOLDER IN  

となるようTN端子同士を結線したので、どれにもプラグが刺さってない時は音声IN/OUTとゲート信号のケーブル3本で済みます。  
VCAだけ、EGだけ使いたいという事が割とあるのでこれが出来るのは便利です。  

## 実装後

![img](/assets/photos/20240718_20240718-IMGP8804.jpg)  

表面にプリントラベルをはって印字で役割を示しつつ穴が空いたままの部分を隠しました。これなら見栄えもなんとか。  
回路図には載せてませんがEnvelope出力を使ってLEDを点灯させています。  

![img](/assets/photos/20240718_20240718-IMGP8823.jpg)  

サイドはこんな。  

![img](/assets/photos/20240718_20240718-IMGP8827.jpg)  
![img](/assets/photos/20240718_20240718-IMGP8840.jpg)  

機能追加後の背面です。改造で配線がごちゃっとなってしまいましたが、機能するのでヨシっ！  

## デモ  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">V13700 VCA + トランジスタによるダブルウェーブフォールドのモジュールになりました！<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/nWZuJBkyyK">https://t.co/nWZuJBkyyK</a> <a href="https://t.co/RwCBKb5L5f">pic.twitter.com/RwCBKb5L5f</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1809958220217340195?ref_src=twsrc%5Etfw">July 7, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

波形の折り返される感じのデモ。  
下の波形が元波形、上の波形がウェーブフォルダーを通した波形。
CV1,2にLFOとエンベロープを入れて音量を変化させ、一定音量以上は波形を折り返して出力しているのがわかると思います。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">VCA+ウェーブフォルダーモジュールの回路内で余ってたオペアンプとトランジスタでエンベロープジェネレータ回路をこさえた<br>簡単なEGで十分かもー <a href="https://t.co/5EegipCvNt">pic.twitter.com/5EegipCvNt</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1813513852899991797?ref_src=twsrc%5Etfw">July 17, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

EG機能のデモ。最初はASRエンベロープ、後半はDecay(Release)エンベロープ。

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">音がたくさん鳴るようになってきたので嬉しい☺️<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/eIIcHQcqto">pic.twitter.com/eIIcHQcqto</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1813908860245332002?ref_src=twsrc%5Etfw">July 18, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

ウェーブフォルダーの音はこれがわかりやすいかも。右上のが今回のモジュールで、サイン波形を通して柔らかい矩形波のような音色に仕上げています。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">思わぬ変化と期待通りの変化の狭間を行き来する感じが楽しい<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/YVVgTzROUI">pic.twitter.com/YVVgTzROUI</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1818253716451123569?ref_src=twsrc%5Etfw">July 30, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

こちらはSAW波形を入れてディストーションめにかかった感じ。  

## 感想  

### ブレボ基板モジュールで作ってみて

* パネル面作成の手間がほとんどない。楽。
* ユニバーサル基板向けの実態配線図書くのを省略出来る。楽。
* ブレボで動いていた回路をそのまま移せるので、実態配線図や回路図清書時の凡ミスを防げる。楽。
* パーツの脚どおしを綺麗に曲げたりしてはんだ付けして組み上げてく必要が基本ない。楽。
* 電源ラインとGND位置がわかりやすいので動かないときにトラブルシュートしやすい。楽。

個人的に今のところメリットしかないですね…ひとつだけ欲しいモジュールなんかはもうこれで十分かも。  

### EG+VCA+Wavefolderモジュールを使ってみて

実は作ってふた月くらい経つんですが（ブログに公開するの遅すぎ）、EGとVCAが一組あるので使いやすいのと、エンベロープで倍音に変化が付くのが単純に楽しくてついつい使ってます。  

![img](/assets/photos/20240806_IMGP8882.jpg)  

[自作ユーロラックモジュラーを始めた時に作ったIKEA箱を使った56HPケース]({% post_url 2023-08-25-diy-eurorack-power-case %}) に、自作モジュールだけ詰め込んでしばらく遊んでいました。  

モジュールは左から、  

* 16StepSeq+ユークリッドリズムトリガー1系統+クオンタイザー
* 2VCO+1LFO
* 64Stepサンプルシーケンサー
* 今回のEG+VCA+Wavefolder
* 4pole LPF
* VCA
* Gate OR combiner
* Mixier + Multipler
* PSU

という組み合わせ。このケースのみでドラム、ベースライン、うわものをシーケンスさせてLFOでモジュレーション掛けつつもつまみグリグリして変化を付けてく遊びが出来ちゃう。これを使ったデモが以下。  

<iframe width="560" height="315" src="https://www.youtube.com/embed/szY7E-fJ0OA?si=frEbbklDvmniA65y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>  

ミキサーとエフェクターは別途使っているけどそれ以外はこのケース内で完結して中々楽しい。  

<iframe width="560" height="315" src="https://www.youtube.com/embed/rw5O4wigSXM?si=zKP_D-YhyGTQNwuW" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>  

こちらは[KATさん](https://twitter.com/Ka_t_)デザインのLPGをVCAと入れ替えて。  
EG+VCA+WAVEFOLDERモジュールのEGをASRモードにして、複数ゲートをORでミックスしたあとにEGに入れて使っています。  

