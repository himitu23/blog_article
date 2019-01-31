# センスウェイさんのLoRaWANスターターキットを試してみた - サーバーレスにセンサデータ取得&可視化

Getting start the Senseway LoRaWAN Starter Kit - get the sensor data and visualize it

やっていくぞ！ということで今回はセンサデータの取得と可視化を試します．

キットなどについての前の記事はこちら．

//リンク

スターターキットに含まれていたのは以下のものでした．

- Arduino Uno互換機
- LoRa (WAN)通信モジュール
  - LoRaアンテナ（800MHz ~ 1kHz向け？）
  - USB-Bケーブル
- LoRaWAN屋内用ゲートウェイ
  - アンテナ
  - Mini USBケーブル
  - USB変換アダプタ
  - LANケーブル
- 温度センサ（ADT7410）

今回はこのADT7410のデータ取得から，その値をMicrosoft Power BIをりようして可視化するまで試してみます．

//目次

## 実際にデータを取得して送ってみる

センサノードのキットなので，LoRaWANでセンサデータを収集しましょう．


#### センサを試す（センサ値取得）

さっき（前の記事）はとりあえず何もないデータを送っていただけなので，センサ値取得も試してみます．

キットに含まれていたのはADT7410というI2C温度センサです（以下はAnalog Devicesのリンク）．

https://www.analog.com/jp/products/adt7410.html

> - 温度精度
>   - ±0.5℃＠-40℃～+105℃（2.7V～3.6V）
>   - ±0.4℃＠-40℃～+105℃（3.0V）
> - 16ビット温度分解能：0.0078℃
> - 高速な最初の温度の変換時間：パワアップ後6ms
> - 温度範囲：-55℃～+150℃
> - 電圧範囲：2.7V～5.5V
> - I2C互換インターフェース

http://akizukidenshi.com/catalog/g/gM-06675

> ​         基板上の入出力端子：４個（ＶＤＤ，ＧＮＤ，ＳＣＬ，ＳＤＡ）

ちなみにピンはLoRaWAN ShieldがそのままUnoに乗ってるので，Unoのピン配置のI2Cを確認．

https://ht-deko.com/arduino/uno.html

つなげるとこんな感じに．

![ConnectPin](https://raw.githubusercontent.com/himitu23/blog_article/master/20190124_IntroSensewayKit/img/img/ConnectPin.png)

KashiwaGeeksのサンプルにあった「I2C_Temp_Sensor_and_GPS_Sample」とwsnakさんのブログを参考に，とりあえずKashiaGeeks上で温度の値だけ取れるようにしてみます．I2Cの利用はWire.hが使えるとか．8bitで値を取得します（16bitでもできるっぽい？）．

https://github.com/ty4tw/KashiwaGeeks/tree/master/examples/I2C_Temp_Sensor_and_GPS_Sample

http://www.wsnak.com/wsnakblog/?p=323

http://www.wsnak.com/wsnakblog/?p=409

改めて作成したサンプルはここに．

https://github.com/himitu23/KiwiLoRaShield/blob/master/I2C_Temp_Sensor/I2C_Temp_Sensor.ino

シリアルモニタでログが見れます．

```
**** I2C_Temp_Sensor_Sample (only get the temp data) *****

_/_/_/ KashiwaGeeks 0.10.3 _/_/_/

Temp=21.31 [C]
Sleep.
Wakeup.
Temp=21.38 [C]
Sleep.
Wakeup.
Temp=24.88 [C]
Sleep.
Wakeup.
Temp=26.19 [C]
Sleep.
```

これでデータが取得できました．これをベースに送信プログラムを作成します．

### LoRaWANサーバへセンサデータを送る＆確認する

取得したデータを送るようにします．これも他のサンプルとかを見て適当に．

作成したプログラムはこちら．

https://github.com/himitu23/KiwiLoRaShield/blob/master/I2C_Temp_Send/I2C_Temp_Send.ino

取得した温度のデータを１分に１回送信します．シリアルモニタログはこんな感じ．

```
**** I2C_Temp_Send *****
try to join... accepted.

_/_/_/ KashiwaGeeks 0.10.3 _/_/_/

Temp=21.75 [C]
2175.00

Send  =>lorawan tx ucnf 16 2175<=

Recv  =>lorawan tx ucnf 16 2175

>> Ok

>> tx_ok

> <=
Error Code(0 is Sccess): 0
Sleep.
Wakeup.
Temp=21.63 [C]
2162.00

Send  =>lorawan tx ucnf 16 2162<=

Recv  =>lorawan tx ucnf 16 2162

>> Ok

>> tx_ok

> <=
Error Code(0 is Sccess): 0
Sleep.
Wakeup.
Temp=21.50 [C]
2150.00
```

あとの視覚化のために送信時のデータフォーマットをちょっといじって10進数にしています．以下の部分です（全体はプログラムをみてね）．

```C
int result = LoRa.sendData(portTemp, ECHO,F("%04d"),(int)temp);
```

Senseway Mission Connectのデバイスの通信量が増えていることを前の記事と同様に確認します．ノードの詳細→通信回数で確認できます．

しかしこれだとセンサのデータの中身を見ることができません．

そこで**Azure IoT Hubに投げて可視化**してみましょう．

### Azureの利用 - 可視化を試す

Azure IoT Hubを利用して可視化サービスであるPower BIを試します（ぐぐったらでてきたので）．アーキテクチャは以下の通りです．

![Arch](https://raw.githubusercontent.com/himitu23/blog_article/master/20190124_IntroSensewayKit/img/Arch.png)

**サーバーレス！**

まずAzure IoT Hubとの連携ができるとのことなので，センスウェイさんの公式サイトのマニュアルを参考に設定とかを行います．

https://service.senseway.net/manual/manual-with-azure-iot-hub/

Azureのアカウントは既に持っていたので，そのままAzure PortalからIoT HubとSenseway Mission Connectを連携設定しました．

連携できると以下のメッセージを確認できます（うまくいかないとErrorを吐きます）．

```
項目	値
外部連携設定	Azure IoT-Hub
外部連携最終結果	2019/01/30T01:32 OK
```

こちらのサイトを参考にAzure IoT Hub→Analytics Jobs→Power BIを繋ぎます（Power BIはマイクロソフトが提供する強力な可視化サービスの一つです）．Power BIは今回無料版を利用しました（メールアドレスは所属団体のものを利用しないといけないので大学のメールアドレスを）．無料版はなんかわかりにくいので注意してください．一部に制限があります．

https://docs.microsoft.com/ja-jp/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi

うまく連携できると，Analytics JobsのMonitoringで以下のようにデータが届いていることを確認できます．寝る前にうまく言って，起きてからノードを電源につないで大学にでかけたので，それ以降データが流れてきていることもわかります．

![AzureIoTHubMetricsCheck](https://raw.githubusercontent.com/himitu23/blog_article/master/20190124_IntroSensewayKit/img/AzureIoTHubMetricsCheck.PNG)

*参考：https://docs.microsoft.com/en-us/azure/iot-hub/tutorial-connectivity*

とりあえず，マイクロソフトの公式リンクの通りに進めると，Power BIでこんな感じに見えました．なお，Azure Stream Analytics JobsをStopするとPower BIにデータは送られなくなります．

![PowerBI_0](https://raw.githubusercontent.com/himitu23/blog_article/master/20190124_IntroSensewayKit/img/PowerBI_0.PNG)

このままだとデータが届くのは確認できますが，肝心の温度データが取れていないので，ちょっと試行錯誤．何もしていない状態だと，JSONの各Elementが取れていますが，これでは何もできないです．もちろんデータの中身も見れません．

ここで取れていたフィールドはSenseway Mission Connect→Azure IoT Hubで流れてきたものです．そこでこの中にデータが入っているはずなので，その中身を取り出す必要があります．

Azure IoT HubにはSenseWay Mission Connectから**なにかしらのJSON Format**で送られているはずです．以下を参考にデータをParseするQueryをAzure IoT HubのQueryに書きます．

https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-parsing-json

JSON Formatはセンスウェイさんが公開していないようだったので，Power BIに最初見えていたElementを元に，適当に当たりをつけて調べました（スターターキットに含まれていた資料のとあるページも合わせて参考にしました）．どうやら主なデータは"mod"に入っているようでした．また複数のgwが受信することも想定しているようですが，JSON Arrayは対応していない？ようなので取れなかったです．なお，今回判明した仕様は下記クエリ以外については私からは公開しません．

AzureのJop topologyに以下のクエリを書きます（#のコメント部分は消してください）．このクエリを書くのがとても大変でした．．．

```
SELECT
    IoTHub.ConnectionDeviceGenerationId,#IoT Hubで割り当てられるID
    IoTHub.EnqueuedTime, #IoT HubにInputされた時刻
    mod.fq,
    mod.cnt,
    CAST (mod.data AS bigint) as Temperature,
    mod.devEUI,
    mod.dr,
    mod.port
INTO
    SensewayVisualizeTestOutput
FROM
    SensewayNodeVisualizeTest
```

*Azure IoT Hubが付与するJSONはこちらを参考：https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-define-inputs*

*Azure Stream Analytics上のSQL Data Types：https://docs.microsoft.com/en-us/stream-analytics-query/data-types-azure-stream-analytics*

*SQLクエリ中の型変換についての参考Q&A：https://stackoverflow.com/questions/45550481/azure-stream-analytics-sql-to-convert-string-to-float*

SELECT文はデフォルトだと「*」と，とりあえずJSONの中身を全て取ってくる仕様だったので，Senseway Mission Connectから取れそうなものを引っ張りました．周波数やメッセージカウントが取得できそうです．**なお，そのままだとmod.dataはStringなので，公式のマニュアルに従ってbigint型へCASTします．ここ大事！**

一度Stream Analytics Jobsを停止後，Queryを書き換えて再度Startします．するとPower BIのフィールドにこんな感じに見えると思うので，とりあえずそれぞれのデータをリスト表示してみました．

![PowerBI_2](https://raw.githubusercontent.com/himitu23/blog_article/master/20190124_IntroSensewayKit/img/PowerBI_2.PNG)

Queryで指定したElementがきちんとロードされており，とりあえずレポートでそれぞれのデータを見ることができました（固定の周波数じゃないのが気になります）．

肝心の温度データは”temperature"に格納されています．ここで，Arduinoのプログラムは10進数でそのまま送るように（20.32度→2032として送信）変更しており，mod.dataの中身は10進数の温度データのみとしています．なので，bigint型にCASTしてそのままPower BIに流し込むことで，すぐに折れ線グラフにすることができるはず！

折れ線グラフを選択し，X軸にenqueuedtimeを，値にtemperatureを選択し，折れ線グラフにした結果はこちら（100倍した値になっています）．

![PowerBI_3](https://raw.githubusercontent.com/himitu23/blog_article/master/20190124_IntroSensewayKit/img/PowerBI_3.PNG)

スケールとかは，ローラーのようなアイコンからいろいろいじれます．なお，Y軸はそのままなので，気温は×100した値になっていますがご愛嬌ということで．これで私の部屋の気温を無事グラフにすることができました．途中でばーんと上昇しているのは私がセンサを握ったからです．物理でバッグです．

これで**実機を利用してセンサ値を取得，可視化することができました！！！**おつかれさまでした

本当は公開したかったのですが，その機能はPro（めっちゃ高い）だけらしいです．ざんねん．

## （おまけ）デバイスから取得したセンサの情報をスマホでみる

Power BIにはElasticsearchのKibanaのように，ピンボードがあり，iOSアプリのPower BIからこのピンボードへとアクセスできます．これでセンサのグラフを遠隔から見れますね．

App StoreでPower BIアプリをダウンロードし，ログインするとダッシュボードが見えます．

![App1](https://raw.githubusercontent.com/himitu23/blog_article/master/20190124_IntroSensewayKit/img/App1.jpg)

今回は「SensewayTest」というダッシュボードを用意してみました．

![App2](https://raw.githubusercontent.com/himitu23/blog_article/master/20190124_IntroSensewayKit/img/App2.PNG)

いくつか表示形式を用意してみました．一番上は折れ線グラフ，その下は現在の値を表示しています．これで自宅の気温が一発でわかります！ｗ

もちろんグラフだけを表示する事もできます．

![App3](https://raw.githubusercontent.com/himitu23/blog_article/master/20190124_IntroSensewayKit/img/App3.jpg)

取得期間がそこまで長くないのでちょっと微妙ですが，とんがっている部分は私が寝るときで，寝ている間〜現在に気温が低下していることがわかります．最後にすこし尖っている部分がありますが，これは私が起きたタイミングです．

## （おまけ）通信費用

センスウェイさんのサイトに価格について明示されています．

https://service.senseway.net/price/

> デバイスあたりの課金金額はその月で一番通信した1日の接続回数で決まり、利用デバイスの課金額の合計で課金請求されます。

```
1日の接続回数	通信間隔の目安	通信形態	税抜金額(円)
12	2時間	上り・下り	30
48	30分	上り・下り	50
144	10分	上り・下り	100
288	5分	上り・下り	200
480	3分	上り・下り	300
1,440	1分	上り・下り	400
```

そんなに安くない気がします．

SenseWay Mission Connectにログインし，デバイス一覧から各デバイスの最大通信量を見ることができます．今回，キットを試した後はこんな感じになっていました．

```
No.	DevEUI	名前	タイプ	ステータス	UpLink
今月最大	DownLink
今月最大	通信量
詳細	備考	編集
1	**********	Senseway LoRa node example	ADB922S	使用中	919	0		
```

これだと300円の繰り上がりの400円くらいでしょうか．この月はどれだけ使ってもこれを下回れば400円ということになります？

## （おまけ）KashiwaGeeksを少し改造

KashiwaGeeksにおけるTASKの実行頻度は"interval by minute"とあるように分単位でしか設定できません（利用用途としては十分です）．

ライブラリのApplication.cppにあるaddTask()では引数の型がuint32_tなので小数点も無理です．．

デバッグがしづらいですね．というわけでちょっと改造しました．

forkして自分のリポジトリにおいています．

https://github.com/himitu23/KashiwaGeeks

Applicationをいじってuint32_tをfloatに変更しています．

これで

```
TASK(taskTemp, 0, 0.1),
```

とすると，0.1分（＝6秒）間隔で実行できるはずです．ただし，デューティサイクルには気をつけてください．電波を扱う場合はお互いにマナーを守りましょう．

※なんか４回目くらいで停止するのでライブラリ側で何かしら制限されているかもしれません

## 終わりに

修論があって，一息ついてから初めたのでちょっと遅くなってしまいました．センスウェイさんにせっかくキットを送っていただいたのに申し訳なかったです．

今回最も大変だったのは．．

- KashiwaGeeksの理解
- Senseway Mission ConnectのAPIフォーマット周辺（仕様を公開したほうがいいと思います！）
  - （参考）The Things NetworkのAPIリファレンス：https://www.thethingsnetwork.org/docs/applications/mqtt/api.html
- Power BIでグラフを表示するためのJSONの変換周辺
- Online Power BIについての情報が少なすぎる
  - Desktop版の資料が豊富です．．

あたりでしょうか．

やり残したこと（研究風に言うと今後の課題）

- 取り出した値を100で割りたい
  - Power BI上で可能？
- 負の値に対応する
  - 送信時に+50して，受信側で再度-50したい
- 消費電力などを調査したい

今回サーバーレスで構築したので，

ちょうど２週間位前からAzureを触り始めていたので，ちょうどいいタイミングでした．Azure IoT Hubは初体験だったので，他のIoT関係でも活かせそうです．

もう少しキットを借りたままでも大丈夫？みたいなので，デバイスの解析やその他サービスとの連携，Node Redとの連携とか試してみたいですね．

１月末まで，との約束だったのでギリギリセーフです！この記事がセンスウェイさんのお役に立てば幸いです．
