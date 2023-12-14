---
layout: post
title: Eight Waveform LFO
date: 2023-12-14 12:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20231214_IMGP8466.jpg
---

![img](/assets/photos/20231214_IMGP8466.jpg)  

## 簡単なLFOをと思ったけど欲張ったら複雑になっていた

治具（前々回）とエフェクター（前回）が出来たところで、次はやっぱりVCOかな？いや先にオペアンプでLFO作ってみたいかもということで「DIY Moduler Synth LFO Schematics」みたいなワードで探索してブレッドボードで色々試しました。以下に参考になったリンクを記載します。感謝！  

* Electronic thing... and stuffのSimple LFO 1.5 [https://www.davidhaillant.com/simple-lfo-1-5/#more-2112](https://www.davidhaillant.com/simple-lfo-1-5/#more-2112)
* Experimentalistsanonymous.comのSimple Dual LFO [https://experimentalistsanonymous.com/diy/index.php?dir=Schematics/Oscillators%20LFOs%20and%20Signal%20Generators](https://experimentalistsanonymous.com/diy/index.php?dir=Schematics/Oscillators%20LFOs%20and%20Signal%20Generators)
 Experimentalistsanonymous.comのLFO [https://experimentalistsanonymous.com/diy/index.php?dir=Schematics/OOP%20Japanese%20Electronics%20Book](https://experimentalistsanonymous.com/diy/index.php?dir=Schematics/OOP%20Japanese%20Electronics%20Book)
 KassutronicsのVariable waveshape LFO [https://kassu2000.blogspot.com/2015/10/variable-waveshape-lfo.html](https://kassu2000.blogspot.com/2015/10/variable-waveshape-lfo.html)
* Syinsi.comのLFO [https://syinsi.com/projects/building-the-lfo/](https://syinsi.com/projects/building-the-lfo/)
 Electrical Engineeringのsync LFOスレ [https://electronics.stackexchange.com/questions/380442/how-to-fix-lfo-sync-by-changing-component-values](https://electronics.stackexchange.com/questions/380442/how-to-fix-lfo-sync-by-changing-component-values)

オペアンプによる積分回路とコンパレータを組み合わせた発振回路で三角波と矩形波を取り出す。キャパシタと抵抗の値で周波数が決まるので、可変抵抗で任意の周波数に調整する感じ。  
キャパシタの切り替え（あるいは並列に追加する）スイッチを付けてレンジを切り替える機能を追加してたり、サイン波形の回路を追加したり、バイポーラ/ユニポーラの振幅域に違いがあったり、波形が可変したり…と簡単に出来るものでもバリエーションがいろいろあります。  

![img](/assets/photos/20230924_IMG_5982.jpg)  
（試しているうちに回路が大きくなってブレボの上がカオスになる）

色々試してるうちに欲しい機能もまとまってきました  

* バイポーラ出力のものが欲しいけど、作成済みのRP2040使用モジュールはユニポーラしか受け付けない（マイナス側は反応しない）のでユニポーラ出力の対応もしたい
* バイポーラ出力で10Vpp程度、ユニポーラ出力で5Vpp欲しい
* サイン波も欲しい。多少ひずみがあっても曲線を描いているならOK （ひずみ率よりシンプルな回路を優先）
* 十秒単位の遅い周期が欲しい（フィルターをゆっくり可変させたりしたい）
* シンクは装備してみたいかも
　自作のケースに収まるように幅も奥行きもコンパクトにしたい

という感じでいきたいなと。  

## 仕様
* 6HP
* ±12V(実測各18mA)使用(10pin)
* 入出力
    * 約10Vppバイポーラ 矩形波、三角波、サイン波の3出力
        * Shape調整でパルス波、ノコギリ波(up/down)、サイン波も同様に変形波(up/down)を出力。合計8波形
    * Sync入力
    * 1系統のゲイン・オフセット調整用入出力
        * 出力～1倍
        * オフセット調整～+5V程度
        * 各波形の出力を挿してユニポーラ化して使うことを想定
        * アッテネータ、直流出力としても
* 操作系
    * LFO本体機能
        * Range(LOW/HIGH)スイッチ
        * Frequency、Shapeノブ
        * LOWで30秒程度～10Hz程度、HIGHで10Hz程度～2Khz程度
    * ゲイン・オフセット調整機能
        * Gain、Offsetノブ
        * 調整確認用LED
            * プラス方向、マイナス方向の2系統。出力プラスマイナスいずれか数百mV程度以上で発光開始

回路図をgithubに置いています。  
[https://github.com/marksard/EightWaveformLFO/tree/main](https://github.com/marksard/EightWaveformLFO/tree/main)

![img](/assets/photos/20230930_IMG_5998.jpg)  
（配置を考えるの図）

最初はユニバーサル基板とアルミパネルで作ろうかなと思っていたんですが、回路を書いてると配線が入り組んでてユニバーサル基板で組もうとすると基板が大きくなるなと思ったので基板を発注することにしました。  

で、ポット4つにスイッチ1つにジャックが5つもあって場所を取るし、横にも広げたくないので2階建てにしました。1層目はインターフェース基板、2層目がLFO回路基板で、層間のインターフェースはソケットとピンでパラレルに繋ぎます。  

このソケットとピンにどの信号を出し入れするか考えてるウチに、このインターフェース基板とパネルって使いまわせるよね？と思って、LFO基板の代わりに装着できるユニバーサル基板も作ってみることにしました。ジャックもポットも多いから使いでもきっとあるよね？という思惑もあります。  

![img](/assets/photos/20231214_IMGP8466.jpg)  
（いよいよモジュラーシンセっぽい見た目になってきました）

![img](/assets/photos/20231214_IMGP8471.jpg)  
（二階建て。層間10～11mmくらいになります）


回路は例に漏れずオペアンプによる積分回路とコンパレータを組み合わせた発振回路がベースです。  
パルス生成は矩形波のプラス側とマイナス側をダイオードで切り分けて可変抵抗でミックス。フィードバックループに入れることで三角波も変形、その変形にコンパレータも従って…というもの。それぞれのダイオードをオペアンプでバッファしているのは超低速域でパルス幅が短くなってしまうのを防止するため。  
サイン波は三角波をベースにダイオードでサイン波に近似させるというもの。  
シンク回路はCV→パルス波に加工→FETでスイッチング→キャパシタをリセットという流れの回路です。  

バイポーラ出力は10Vpp(-5V~+5V)で揃えるために、矩形波は5Vツェナーで、三角波とサイン波は半固定抵抗で10Vppに調整します。（5Vのツェナーを見かけなかったので私の試作では5.1Vのツェナーですが）  
5Vppのユニポーラ出力 (0~+5V)は1系統の変換回路を通して行います。これだと全部別々に回路を用意しなくていいし、インターフェースを外出しすることでモジュールとしての応用も効くかなと。  
調整は効きを確認しながらな感じです。一応目視でもざっくりと確認出来るように＋側、ー側それぞれ1個ずつLEDを付けました。LEDの明滅加減でオフセット量や減衰量を見ながら調整出来ます。  


![img](/assets/photos/20231214_IMGP8477.jpg)  
（回路図上では半固定抵抗は3つなんだけど、あとから改造してオフセット最大値がきっちり5Vになるように調整用の半固定抵抗を追加。基板を組んでからも定数の微調整で部品交換したのであんまり綺麗ではないです…）  

![img](/assets/photos/20231214_IMGP8506.jpg)  
（パネルとインターフェース基板を使いまわせるようにユニバーサル基板を作ってみた）


## 使用感など
動作確認機としても使っているbehringer neutronのVCFやVCAを揺らしたりして遊んだり、syncしてバキバキな音になるのを楽しんだりしてたんですが、このあとに作ったモジュールの動作確認に使えたり、ゲイン・オフセット調整機能をLFOとは別の入力から入れてアッテネータとして使ってみたりと役にたってて先に作ってよかったなと思いました。ただ次に作るならこうするかなと思う点もちらほら。  

### syncは不要かも  

波形眺めて遊ぶぶんには楽しいけど実用は？という感じがした  

### パネルのレイアウト  

左につまみ、右にジャックというレイアウトはケーブルが繋がってるとめっちゃ邪魔で操作しにくい  

### オフセット出力回路を…

オフセット出力回路にMAX5Vに調整するための半固定抵抗が欲しい（基板を改造して増設した）  

### LED

LEDでの確認は±20mVくらいから見たいし、一定以上は光の強さが変わらないので±にもう1個ずつ追加でLED欲しい  

という感じ。もっとシンプルにして4HPくらいに収めたいなと思ったりもするので、他のモジュールを一通り作り終えたらこのLFOも再設計してみたい。  
