---
layout: post
title: "天気予報付き時計"
date: 2018-05-24 10:00:00 +0900
tag: [linux,raspberrypi,hardware]
---

# 出来た
![img](/assets/photos/20180524-APC_0426.jpg)

ほぼほぼ設計通りに仕上がりました。  
ややこしい処理もなかったのでPyQtを直書きしました。ソースは[github](https://github.com/marksard/ClockWithWeatherForecast)に。  
購入したBME280が認識しなかったりとかもありましたが一番手間かかったのがQLabelの大きさに合わせて中のフォントのサイズを変更するロジック。  
現物見ながらフォントサイズと格子の大きさを個別に微調整するの面倒だと思ったので先に組み込んでおいた。結果的にやってて良かった。  
天気アイコンはMeteoconsというフォントを使いました。なかなか見やすくてgood。  
