---
layout: post
title: モジュラーシンセ作りたい
date: 2023-05-29 17:30:00 +0900
tag: [hardware, eurorack, synth]
image: 
---

[Mozziライブラリでシンセサイザーを作る]({% post_url 2023-05-08-make-mozzi-synth %}) で言及しそびれているんだけど、これモジュラーシンセ作りたいなというのが発端なんですよね。  
今回はモジュラーシンセ作ってみたい自分のためにざっくりと決まり事をまとめときます。  

## EURORACK規格
モジュラーシンセにはEURORACK規格というのがある。  
昔の所謂「タンス」の時はそんな規格はなかったが、doepfer a-100が出てからはこれをベースに作られている。  

* https://en.wikipedia.org/wiki/Eurorack
* https://sdiy.info/wiki/Eurorack
* https://doepfer.de/a100_man/a100m_e.htm
* https://intellijel.com/support/1u-technical-specifications/

参考: https://fukusan.com/products/doepfer/a100_bs2/

## 規格のおおざっぱな解説

### 幅、高さ、奥行

元来19inchラックの3Uサイズを1単位として、各種モジュールを収める設計のようだ。  
なので高さそのものは3Uだが、モジュールを設計する際には以下となっている。  

* パネル設計高さは128.5mm、PCBは110mm以内
* 幅は1HP(5.08mm)単位で呼ばれる
  * ノブ、スイッチ、ジャックの数で必要幅がだいたい決める感じ
* 奥行きは25mm～40mm程度がskiffケース（浅めでデスクに上向きで使うようなタイプのケース？）に対応するよう感じらしい

### 取り付けネジ

M3x6mmくらいが標準っぽい

### 電源

* +12V、-12V、5V

が電源モジュールによって用意される（許容電流は電源モジュールによる）  
16ピンヘッダでリボンケーブルで接続する感じ（10ピンタイプもあるらしい）  
ピン位置の規定はあるが、接続の際はテスタで確認したほうがいいらしい  

### モジュール間インターフェース

* ノートON/OFFを伝えるGATE  
	* 0V or ～5V　立ち上がりがON、0VでOFF  
* モジュール間のコントロールを伝えるCV  
	* 0V～5V  
* 音の高さを伝える用のCV  
	* V/OctaveとV/Hzがあるが、EurorackはV/Octaveのみ？  
	* 8オクターブまで伝えられるようにと0V～8Vくらいが規定されている模様  
* テンポ情報を伝えるSYNC  
	* 0V or ～5V　立ち上がりが起点  
		* 元の発想はDIN SYNCと思われる  
			* https://ja.wikipedia.org/wiki/DIN_Sync  
			* こっちはスタート* ストップ信号もあった  
		* 単位はPPQN(Pulse Per Quater Note)  
		* 2,4,24,48PPQNあたりがある  
			* Teenage EngineeringのPOシリーズは2PPQN  
				* ていうか2PPQNってどうやって16ステップの間隔制御してるん…？パルスのON時間読んでいる？  
		* 4PPQNで4/4拍子でいう16ビート相当のパルスが来る  
		* パルスはシンメトリ(50%)とwikiには書いてるんだけどTeenage Engineering PO-32もパリピもRoland T-8もそんなことなかった…  
		* 1PPS（Pulse per sec？）みたいなのもあるけど使ったことはない  
* オーディオ出力  
	* -5V～5V　～10Vpp（規定では）  
	* オーディオ出力をGATEやCVとして突っ込むというのは当然あるので、CV/GATE入力には過電圧保護を入れたほうが良い  

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
