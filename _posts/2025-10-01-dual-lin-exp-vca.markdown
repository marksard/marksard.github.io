---
layout: post
title: SSI2164 Dual Linear/Exponential VCAモジュール
date: 2025-10-01 10:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20250827_IMG_9426.jpg
---

![img](/assets/photos/20250827_IMG_9426.jpg)  


[先日作った AS2164(SSM2164) Dual Variable Curve VCA](https://marksard.github.io/2024/10/07/epmkiii-as2164-vca/) をコンパクトにしたいなとなりまして。

--- 

回路図、諸元など→ [github](https://github.com/marksard/DualLinExpVCA/tree/main)

## ハードウェア

### 仕様
- 4HP  
- ±12V  
- VCA 2基  
  - レベルポット（IN1のみ）  
  - CV調整ポット  
  - 減衰カーブ切り替えスイッチ（リニア/指数）  
- DCカップリング  

## 回路の概要

![img](https://raw.githubusercontent.com/marksard/DualLinExpVCA/refs/heads/main/manual/img/schematic01.png)  
![img](https://raw.githubusercontent.com/marksard/DualLinExpVCA/refs/heads/main/manual/img/schematic02.png)  

先日作ったVCAの回路をベースにして以下を変更：

- リニア/指数可変機能を**連続可変式からスイッチ式**に変更  
  - 分圧しないで通すとリニア、分圧を通すと指数  
  - 分圧比は先日の実機（10kポット + 4.7k）で検討しほどほどの設定値に。（SSI2164チップそのままの指数設定ではないです）
- 調整機構の簡素化  
  - CV入力レンジ調整：CV最大時のゲインを設定  
  - IN1最大レベル調整：IN1 LEVELポット最大時のゲインを設定  
- CV入力レンジ調整回路を IN1/2 共通にしたため、部品誤差の影響で百mV程度のズレは発生するが許容範囲  

## 感想

VCAって試したいことがあるときに限って空きがないというのがよくあって細々増減出来そうな4HPサイズで2系統欲しい、という要件で作り始めてパネルレイアウトを考えてリニアだけを予定してたんですけど、スイッチを隙間に綺麗に押し込められて切り替え出来るようになったのはうれしい誤算。

切り替えスイッチが良い感じに収まってるのがわかると思います。スイッチは秋月でも取り扱いのあるショートタイプで、テコの原理上固く感じるけどポット操作やケーブル挿抜に邪魔しないようにと考えて選定。

![img](/assets/photos/20250827_IMG_9427.jpg)  

これまで作ったVCA。左から  

- V13700を使った4HPサイズの1chリニアVCA
- AS2164を使った8HPサイズの2chカーブ可変VCA
- 今回のSSI2164を使った4HPサイズの2chカーブ切替VCA

リニアだけで良いならV13700で作り直してもいいなと思ってたんですけど、秋月で買えなくなって入手性がちょっと悪くなってしまったんですよね。

![img](/assets/photos/20250827_IMG_9428.jpg)  
