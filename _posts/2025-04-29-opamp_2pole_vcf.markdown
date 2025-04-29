---
layout: post
title: OPAMPを使った2-poleVCFの作成
date: 2025-04-29 20:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20250412_20250412-IMGP9018.jpg
---

去年の冬からソロキャンプ趣味を初めたりキーケットの準備も挟まりと自作モジュラーシンセはしばらくまったりムードだったんですが、気になってたヤツ作りました。  

<iframe width="560" height="315" src="https://www.youtube.com/embed/ITJX9jP-zm4?si=iX_gNWLdzJEJHKHI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>  

Moritz Klein氏のVactrol-Based VCF。  
2poleサレンキー型フィルターの回路にカットオフ周波数を決定する要因となる2つの抵抗をバクトロールに置き換えてカットオフ周波数を電圧で可変出来るようにしたもの。  

まずオリジナル通りに作ってみたのですがバクトロールの変化量が違うからか（動画内では自作のバクトロールを使用している）手持ちのLCR0202で使うと扱いにくかったので改造。  

### 回路図

![img](/assets/photos/202500308_2poleVCF.png)  

改造のためサレンキーフィルター回路を調べてるとブックラ292やKORG MS-20のフィルター回路もみかけたけど、今回の元ネタもこの流れを受けてるのかな。  

バクトロールは秋月電子にあるアナログフォトカプラのLCR0202かLCR0203を使用、オーディオ入力は5～10Vpp程度、CV入力3～10V程度を想定。  
バクトロール2つとインジケータLEDは直列にして、ポットにオフセットを入れて少し下駄を履かせた。  
オーディオ入力は1/6に落としてフィルター回路に入れ、終段で元の音量に。フィードバックでの発振時に音量が結構変わる関係もありボリュームポット付きに。  

フィードバック回路には制限を掛けない素の状態と片側だけダイオードクリップする状態を切り替えられるように。Moritz氏の回路やMS-20などにはダイオードなりLEDで入れて2V付近でクリップさせていて、おそらく入力がそこまで高くないから軽くクリップする程度なのかもしれませんが、私のは入力が大き目でディストーションっぽい雰囲気になるのが面白いなと思ったのと、前者も強烈に発振するのが楽しいなと思ったので…（オペアンプ壊れるかな…）  

フィルターは机上で数Hz～11kHz程度の可変域で全開でも少しフィルターがかかっている状態。  

以下はその計算のグラフ。  
キャパシタを68nF、抵抗を200Ω(フィルタ全開程度)と100kΩ（結構閉じた状態）で、Kを可変しています。
コードそのものはgistに貼り付けておきます。最初はchatGPTにやってもらったけど、計算合わなかったので結局自分で調べて書き直したので間違ってたら指摘いただけると勉強になります。  

[グラフ計算コード](https://gist.github.com/marksard/3535ce4f8997536d0c7c4cd340232138)  

![img](/assets/photos/2025-03-17_161258.png)  
![img](/assets/photos/2025-03-17_145729.png)  

観ているとスロープちゃんと12dB/octになってるなーとか、カットオフ周波数の計算式って次数関係ないから実際落ちている場所とはちょっと違うんだなとか、の割にレゾナンスのピークはちゃんとカットオフ周波数の線上に現れるんだなとか…にわか知識なのがバレますね。  

### 実機写真

今回もモジュラー化するにあたって自作モジュラー基板キットのEurorackPlaygroundMkIIIを使いました。  

![img](/assets/photos/20250412_20250412-IMGP9018.jpg)  

パネルへの機能の配置の位置やラベルはほぼWavefolderと同じ。  

![img](/assets/photos/20250412_20250412-IMGP9020.jpg)  
![img](/assets/photos/20250412_20250412-IMGP9021.jpg)  
![img](/assets/photos/20250412_20250412-IMGP9025.jpg)  
パネル面のほとんどの操作系をとってくる必要があったので仕方ないとはいえ線材だらけに…  

![img](/assets/photos/20250313_IMG_8736.jpg)  

移植作業直前の写真。ブレボに回路をギュッと敷き詰めて基板に移してはんだ付けしていくんだけど途中間違ってないかいつもヒヤヒヤする。いやまぁ実態配線図を書き起こして机上デバッグするよりは全然楽なんですけど。  

![img](/assets/photos/20250315_IMG_8744.jpg)  

で、LPFをみちみちに組んだら空きが出来たのでWavefolderモジュールの時と同じEG回路を押し込みました。パネル上に余ってたポットが1個しかなかったのでディケイだけですが十分使える。


### デモ

機能デモをやったつもりだけど、EGのモードがSustain-Release固定だったですね…  
左のLEDはバクトロールと同期しててカットオフポットやLFOも混ざってるので、このLEDが光っている＝バクトロールの抵抗も変化しているという感じなので視覚的にもいいかなと。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">デモ<br>左上からから下に、FREQ,RESO,RESO CLIPPER SW,CV1(内蔵EGに内部接続),CVインジケータLED,GAIN,EG(Decay/Release time), EGインジケータLED,Decay/Release SW, CV2(外部LFOに接続)<br><br>左隣のDCOからのノコギリ波の音を変化させてそのままミキサーへ流してる<br>省スペースでベースの担当させられそうね <a href="https://t.co/4cHYKKaFxP">https://t.co/4cHYKKaFxP</a> <a href="https://t.co/5YVzmEqdcm">pic.twitter.com/5YVzmEqdcm</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1900841412066972032?ref_src=twsrc%5Etfw">March 15, 2025</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

こちらはEG回路入れる前のデモ。  
コードVCOを入れて、出力はミキサー→Sprout（エフェクター）、CV1にwavefolderモジュールのEGを、CV2にはLFOをいれて揺らしています。  
ポットのスイートスポットがちょっと狭くて調整がムズイんですけど、良い感じにスイープすると気持ちいい。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">ブレボで組んでたオペアンプ+バクトロールLPFをモジュラー化した記念🎛️<br>なかなか好きな感じの出音<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/uUBL26QLiU">pic.twitter.com/uUBL26QLiU</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1900392980751516019?ref_src=twsrc%5Etfw">March 14, 2025</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

こちらはいろいろ混ぜての演奏。  
今作のLPFとwavefolderに同じ三角波を入れてランダムシーケンサーのゲートと、シフトレジスタのゲートでそれぞれ発音させて疑似的に別の音的に扱ってます。コードVCOはパッド的に鳴らしっぱにして、上にアルペジオ的な何かを鳴らしてあとは音の出し入れで押し通した…という感じ。演奏技術は…修行します💦  
3パートのパターンは、シーケンサーアプリのランダムシーケンサーのCV/GATE、ユークリッドリズムトリガー、内部LFO→内臓S&H→内臓クオンタイザーで取り出したCV、ユークリッドリズムで動かしたシフトレジスターアプリのR2R出力→内臓クオンタイザーで取り出したCVとシフトレジスタのトリガーと、合計3組のGATE/CVをバラバラに組み合わせて鳴らしています。  
ユークリッドリズムのタイミングを変えるとR2RでのCVパターンやトリガーパターンが規則的に変わるのが楽しい。こういうトリガーモジュールを組み合わせて紡ぐの楽しい。  

<iframe width="560" height="315" src="https://www.youtube.com/embed/vW5jBLbyDdg?si=1Iqju2ZIwwvDJ021" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>  

ちなみに一番最初はトランジスタ2石で作る2pole LPFの抵抗をバクトロールに変更して試してた。  

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">トランジスタ2石で12dB/octなLPF<br>ブレボだから不安定なのか、こんなんでええんやろがい定数だからかはわからんが、兎に角雰囲気は出てる笑<br>オペアンプに比べるとコンパクトにはおさまるよね。片電源で動くし <a href="https://t.co/ybps3dUTXS">pic.twitter.com/ybps3dUTXS</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1887499098485412278?ref_src=twsrc%5Etfw">February 6, 2025</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

初回組み立て時。  

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">昨日に続いて…今日はオペアンプ使ったアクティブフィルター12dB/oct<br>スイートスポットが狭過ぎてナーバスだねぇ…ピーキーな感じは好きだけど <a href="https://t.co/alYgMTRdUi">https://t.co/alYgMTRdUi</a> <a href="https://t.co/SAdcWLVpAc">pic.twitter.com/SAdcWLVpAc</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1887866395901431902?ref_src=twsrc%5Etfw">February 7, 2025</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

EP mkIIIに移植するためにブレボ上の配置を詰めたあとの動作確認。  

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">しばらく放置してたLPF、最近またちょびちょびいじってて、そろそろ基板に乗っけようかなと思ってる <a href="https://t.co/ftzFXD7gT7">pic.twitter.com/ftzFXD7gT7</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1898370293351289086?ref_src=twsrc%5Etfw">March 8, 2025</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  


### 感想など

出来上がったのを実測するとフィルター全開レゾナンス最大で8kあたりにピークが出てた。  
おそらくオペアンプの能力不足で全開でバクトロールのLEDを光らせても想定より低い抵抗値になってないご様子…CVミキサー回路のオペアンプをNJM4580とかのもう少し馬力のあるオペアンプに変えたほうが良いのかもしれない。  

以下はバクトロール内の想定抵抗200Ωを300Ωに変更したグラフ。やはりそういうことなんだろう。  
![img](/assets/photos/2025-03-17_145635.png)  


デモやら録ったあと、やっぱり全開で開ききった感じがしないのが気になったので33nFに変更。下はそのグラフ。実測しなくても音の明るさが増したのでヨシ  
![img](/assets/photos/2025-04-29_201805.png)

ポットやCVでの調整がやはりまだピーキーさが残ってて癖が強いんですけど4-poleローパスほど急峻じゃなく、減衰はかなり急峻なもののLPGっぽさがあってトリガーパルスみたいな短いゲートを入れるとプラッキーなサウンドになるのが何気に良い。（CVもカットオフのポットもAカーブのほうが良いかもしれない…）  
このLPGっぽさとEGを付けたおかげで、とりあえずオシレーター波形とゲート突っ込めばVCA不要で鳴るのでササっと試作物の動作確認に使えるのが便利。  
