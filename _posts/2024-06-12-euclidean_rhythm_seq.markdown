---
layout: post
title: ユークリッドリズムシーケンサーを作った
date: 2024-06-12 18:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20240612_IMGP8710.jpg
---

![img](/assets/photos/20240612_IMGP8710.jpg)  

## マルチティンバー音源化

モジュラーシンセって音が出るモジュールが増えるとデチューンさせて一斉にぶわぁ～っと音を出すよりパート分けたりモジュレーションソースとして使いたい、という思考になるんですね。  
ベースとアルペジオとバスドラの音を作って一緒に鳴らしたいというマルチティンバー音源的な欲…  
鳴らしたい方向性の音や好きなモジュールを好きなケースに詰め込んでひとつのマルチティンバー音源楽器として組んでいく。でっかくて重たいガジェットシンセ（）を作る感じ。そういう面白さも秘めているんだな。  

さておき、とりあえず自作の16ステップなシーケンサーアプリのSYNC OUTを1PPQ設定にしてそれをトリガーにバスドラムに仕立てた音を一緒に出してみたりして遊んでたものの、追加でトリガー出したり演奏出来るシーケンサーが欲しいな！となりました。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">クロックソースをディバイドするのと、ユークリディアンなトリガー出すの欲しいけど、リズム系のモジュールがやっぱり欲しくなってくるよなぁ…<br> もうVCVで遊ぶでええやん？となる（苦笑 <a href="https://t.co/OEUVASOi2e">pic.twitter.com/OEUVASOi2e</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1789126282984521917?ref_src=twsrc%5Etfw">May 11, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

クロックディバイダーから作るかなぁ、などとフリーなモジュラーシンセアプリのVCVで遊んでたらひらめきました。  

* ユークリッドリズムのトリガー出力機能
* サンプル＆ホールド機能
* クオンタイザー機能

くらいならひとつのアプリとしてまとめられそうだなと。  
ユークリッドリズムシーケンサーは作ってみたかったものの一つだし、サンプル＆ホールドとクオンタイザーもあるとやれることもグッと広まるし。  
内部での機能的な繋がりは  

* 内部 or 外部クロックから、ユークリッドリズムのトリガーを出力
* 内部LFO or 外部CVをソースに、内部 or 外部クロック or ユークリッドリズムトリガーでサンプル＆ホールドする
* サンプル＆ホールドした値はクオンタイザーに通してCV(V/OCT)を出力

という感じ。  
ユークリッドリズムのトリガー出力だけ使うもよし、クオンタイザー通したCVと組み合わせて鳴らすもよし、CVのソースにしてもよしと一粒で何個も美味しいみたいな奴になるなと。  

ちなみに、ユークリッドリズムを超絶ざっくり解説すると、リズムのタイミングに一定のパターンを用いたらなんか大体の民族音楽に当てはまったよ、そのパターンの作りかたがユークリッドの互除法に似てるからそう名付けたよ、という（因果がどちらかは斜め読みでは読み取れませんでした）  
[http://cgm.cs.mcgill.ca/~godfried/publications/banff.pdf](http://cgm.cs.mcgill.ca/~godfried/publications/banff.pdf)  
わかりやすく解説されてるこの方のブログが参考になりました。  
[https://fantasysound.net/2019/07/28/euclidean-rhythm/](https://fantasysound.net/2019/07/28/euclidean-rhythm/)  

ついでに…サンプル＆ホールドはソースとなる電圧をトリガーON～ON間一定に保持するもので、ノイズをソースにするとトリガーするたびに違う電圧値（いわゆるランダム）になる。LFO波形をソースに一定のタイミングでトリガーしていくと、波形の周波数がトリガー周期より遅いとビットが荒くなったように階段状になって、逆に周波数のが速いと一定周期で同じ音が取れフレーズに聞こえる周期が出てきます。  
このホールドした電圧を音階として調律するのが（ピッチ）クオンタイザー。入力した電圧を指定の音階に従った値に補正して出力、VCOなんかに入れるとその音階で鳴る。  
まとめると連続値の波形をサンプリングして離散値に変換後V/OCT式の音階に値を補正するという感じ。雑に言うなら交流→V/OCT変換器的な感じ？  

## 仕様

* ユークリッドリズムシーケンサー部2系統
    * 内部・外部クロックで動作
    * ステップ数1～16
    * トリガー数 1～16
    * トリガーデュレーション(クロック幅に対して5〜100%)
* S&H部
    * ソース
        * 内部LFO(square, up-saw, down-saw, triangle, noise)
        * 外部CV入力(0~5V)
    * ホールドトリガー
        * 設定中のクロック
        * ユークリッドシーケンサーのトリガー
* クオンタイザー部
    * スケール設定 (メジャーとかマイナーとかブルースとか)
    * S&Hからの入力に対するオクターブ変化範囲制限(内蔵LFO時のみ、外部CV時は外部アッテネータで制御)
* インターフェース
    * IN
        * S&H Source
        * Clock
    * OUT
        * Euclid Trigger x2
        * V/OCT

1から作らなきゃだったのはユークリッドリズムのアルゴリズムくらい。内部クロック生成やクオンタイザーのスケール設定はステップシーケンサーアプリから、内部LFOはVCOアプリからの流用。サンプル＆ホールドはトリガータイミングのときだけ出力を変える条件文だけなので機能として作るまでもないという感じ。  

ソースはgithubに置いています。  
Euclidean.hppがユークリッドリズム生成のコア。ステップ数と打点数（それぞれ上限16）を設定して生成します。計算時は演算簡略、メモリ節約のため最大ステップ数16から2バイト（16ビット）におさめて1打点1ビットにしてます。32ビット変数に変えればひょっとしたら32ステップまでいけるかもしれない。参考になればいいですが。    

[https://github.com/marksard/EurorackPlayground/tree/main/app/EuclideanSeq](https://github.com/marksard/EurorackPlayground/tree/main/app/EuclideanSeq)  


ハードウェア的に上に書いた仕様だとIN x2、OUT x3必要で、自設計のプラットフォームモジュールはIN x2、OUT x2が上限で足りないので余っている基板を改造して一台作成。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">マイアプリ用汎用モジュラー、入出力もう一個欲しいし出力ももう一声な感じなので次の設計やる前にアプリの検討も兼ねて改造版を作成<br>midi in◯して普通の入力に変えてopampを12V単電源から±12Vに変更、出力のbi/uni切り替えをアプリで出来るようにセルフハック<br>最後の写真の左上から110, 110改, 100 <a href="https://t.co/MB6BUvCjmu">pic.twitter.com/MB6BUvCjmu</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1790229611462496539?ref_src=twsrc%5Etfw">May 14, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

ちな改造箇所  
　バッファ部+12V単電源→±12V両電源（USBのみでの駆動を視野に入れて5V/12V切り替え式にしたけど結局ほとんど使わなかった）
　1次フィルタ→2次フィルタ（promicroシンセ試作時は2次だったけど1次でいいやと変更、でもやっぱり最低2次必要というのがわかった）
　アプリからのバイポーラ/ユニポーラ切り替え対応機能
　MIDI INをGPIOに制限抵抗と過電圧保護つけた入出力兼用回路に変更（MIDI IN/OUTを使ってなかったので…）

という感じ。  

## 動画とか

トリガーの出具合と入力したCVに対するサンプルホールド後の出力がわかります  
<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">ユークリッドリズムのアプリ<br>ロジック把握した感じCだとめんどくさそうだから最初chatGPTにやらせてみてたんだけど、いい感じに書いてもらうプロンプトを練る時間考えて自分で書いた💦<br>こう言うトリガーあるといくらでも聴いてられるねぇ<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/hjADkdrJw7">pic.twitter.com/hjADkdrJw7</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1790679834013880394?ref_src=twsrc%5Etfw">May 15, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">結構いい感じの詰め詰めユーティリティアプリが出来た！<br>・ユークリッドリズム出力(デュレーション調整可能)<br>・サンプル&amp;ホールド(内部ランダムかCV入力から選択、トリガーはクロック入力かユークリッドから選択)<br>・S&amp;Hはクオンタイザー通して出力(スケール7種から選択)<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/M6591VsJvZ">pic.twitter.com/M6591VsJvZ</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1790754660942581933?ref_src=twsrc%5Etfw">May 15, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

これは16ステップシーケンサーアプリと繋いでアンビエントしてみたもの  
モジュールが増えてくると鳴らし方のバリエも増えるのでかなり楽しい  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">ハギヲさん作ユークリッドリズムシーケンサーのクルクルする画面のやつ真似したい！って思って作りました<br>視覚的にわかりやすくなるのでとても良き<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/TnbXEzh8VU">pic.twitter.com/TnbXEzh8VU</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1791073073229418569?ref_src=twsrc%5Etfw">May 16, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

オシロ機能を載せてみた。サンプルホールドのソース波形の電圧値の最低、最高値を確認しながらV/OCTのオクターブ範囲を調整するためと、入力が0～5Vなのでオフセット値を確認するため。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">簡易オシロスコープを作り直して載せやすい形にした<br>他の処理と併せてポーリングで溜め込むタイプなのでアバウトだけど他の機能と併用出来る<br>内部で作ったLFOやS&amp;Hの波形(12bit数値)も流し込めるからアプリ開発時のデータ確認とかにも使えるかも？ <a href="https://t.co/OcPtgasjBN">pic.twitter.com/OcPtgasjBN</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1795038849904603281?ref_src=twsrc%5Etfw">May 27, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">ドローンというかアンビエントというか？<br>モジュールのLEDに黄色と緑をふやして楽しい<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/vWPcL0T51F">pic.twitter.com/vWPcL0T51F</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1796543739139387801?ref_src=twsrc%5Etfw">May 31, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  

今年の春先にdotRed Audio DesignsのWORKFRAME84-Cのケースを買ったんですが、明らか足りないので先日ArturiaのRackbrute 6Uを買い増ししました。  
この子をこの場所に置くためにデスクまわりを模様替えして奥に棚を置いたりモニタースタンドを右にオフセットしたりしました。  
RackBrute 3Uも買って組み合わせたいなぁ…  

![img](/assets/photos/20240612_IMGP8712.jpg)  
