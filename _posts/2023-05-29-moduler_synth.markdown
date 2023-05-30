---
layout: post
title: モジュラーシンセ作りたい
date: 2023-05-29 17:30:00 +0900
tag: [hardware, eurorack, synth]
image: 
---

[Mozziライブラリでシンセサイザーを作る]({% post_url 2023-05-08-make-mozzi-synth %}) で言及しそびれているんだけど、これモジュラーシンセ作りたいなというのが発端なんですよね。  
今回はモジュラーシンセ作ってみたい自分のためにざっくりとまとめときます。
もし間違え得ればご指摘いただければありがたく🙏

## EURORACK規格

モジュラーシンセにはEURORACK規格というのがある。  
昔の所謂「タンス」の時はなかったようだが、doepfer A-100が出てからはこれをベースに作られている（もちろんタンス時代のものとはサイズが異なる模様）  

* [https://en.wikipedia.org/wiki/Eurorack](https://en.wikipedia.org/wiki/Eurorack)
* [https://sdiy.info/wiki/Eurorack](https://sdiy.info/wiki/Eurorack)
* [https://doepfer.de/a100_man/a100m_e.htm](https://doepfer.de/a100_man/a100m_e.htm)
* [https://intellijel.com/support/1u-technical-specifications/](https://intellijel.com/support/1u-technical-specifications/)

参考: [https://fukusan.com/products/doepfer/a100_bs2/](https://fukusan.com/products/doepfer/a100_bs2/)

## おおざっぱな解説

### 幅、高さ、奥行

A-100は19inchラック（サーバーPCのラックとかと同じやつ）の3Uサイズに専用枠を作成していて各種モジュールを収める設計にしていた。  
なので必要高さそのものは3Uだが、モジュールを設計する際は以下のサイズとなっている。  

* パネル設計高さは128.5mm、PCBは110mm以内
* 幅は1HP(5.08mm)単位で呼ばれる
  * ノブ、スイッチ、ジャックの数で必要幅でだいたい決める感じ
* 奥行きは25mm～40mm程度がskiffケース（浅めでデスクに上向きで使うようなタイプのケース？）に対応するような感じらしい

### 取り付けネジ

M3x6mmくらいが標準っぽい

### 電源

* +12V、-12V、5V

が電源モジュールによって用意される（許容電流は電源モジュールによる）  
16ピンヘッダでリボンケーブルで接続する感じ（10ピンタイプもあるらしい）  
ピン位置の規定はあるが、接続の際はテスタで確認したほうがいいらしい  

### モジュール間インターフェース

* ノートON/OFFを伝えるためのGATE  
	* 0V or ～5V　立ち上がりがON、0VでOFF  
* モジュール間のコントロールを伝えるためのCV  
	* ユニポーラなら0V～8V 
	* バイポーラなら5Vpp(-2.5V～2.5V)
* 音の高さを伝えるためのCV(Control Voltage)  
	* V/OctaveとV/Hzがあるが、EurorackはV/Octaveのみ？  
	* 8オクターブまで伝えられるようにと0V～8Vくらいが規定されている模様  
* テンポ情報を伝えるためのSYNC（これは直接的にはeurorackとは関係ないのかな？）  
	* 0V or ～5V　立ち上がりが起点  
		* 元の発想はDIN SYNCと思われる  
			* [https://ja.wikipedia.org/wiki/DIN_Sync](https://ja.wikipedia.org/wiki/DIN_Sync)
			* こっちはスタート/ストップ信号もあった  
		* 単位はPPQN(Pulse Per Quater Note)  
		* 2,4,24,48PPQNあたりがある  
			* Teenage EngineeringのPOシリーズは2PPQN  
				* ていうか2PPQNってどうやって16ステップの間隔制御してるん…？パルスのON時間読んでいる？それとも二回目のパルスまでの間隔で最後のパルスの間隔からのBPMを保持して…みたいにしている…？  
		* 4PPQNで4/4拍子でいう16ビート相当のパルスが来る  
		* パルスはシンメトリ(50%)とdinsync wikiには書いてるけど本当かなぁ（手持ちの機材はみんなそうではなかったので）  
		* 1PPS（Pulse per sec？）みたいなのもあるけど使ったことはない  
* オーディオ出力  
	* -5V～5V　～10Vpp（規定では…）  
	* オーディオ出力（オシレータ波形などになる）をGATEやCVとして突っ込むというのは当然発生するので、CV/GATE入力には過電圧保護を入れたほうが良い  

### これまでの手持ち機材の調査
　Behringer TD-3のSYNCは5V無くても、2.5V～であれば動くとスペックシートに書いてある  
　Teenage Engineering PO-32のSYNCは1Vも無かった  
　Roland T-8のSYNCはキリッとしてた（何Vかは忘れた）  

### 雑感
上記規定を元に作ると概ね安上がりに自作シンセを互換ありで組めるはず。  
なんだけど、他機種と本当に連携出来るかとかそういう動作確認するためのリファレンスに一台ちゃんとしたモジュラーシンセいるじゃんね？となって躊躇している(笑)  
服買うために着ていく服がいるみたいな。ちょっと違うか。  

MOOGのSUBHARMONICONとか  
MOOGのMother-32とか  
MOOGのMavis  
あたりとベリあたりのユーロラックケースと電源買って、これをリファレンスにしつつ自作するのがいいのかなぁなんて思ったりしている。  
