# センスウェイさんのLoRaWANスターターキットを試してみた - センサデータ取得

やっていくぞ！

ということで動かしてみた記事はこちら．

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

今回はこのADT7410のデータ取得から，その値を？？するまで試してみます．

//目次

## 実際にデータを取得して送ってみる

センサノードなので，LoRaWANでセンサデータを収集しましょう．


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

![ConnectPin](.\img\ConnectPin.png)

KashiwaGeeksのサンプルにあった「I2C_Temp_Sensor_and_GPS_Sample」とwsnakさんのブログを参考に，とりあえずKashiaGeeks上で温度の値だけ取れるようにしてみます．I2Cの利用はWire.hが使えるとか．8bitで値を取得します（16bitでもできるっぽい？）．

https://github.com/ty4tw/KashiwaGeeks/tree/master/examples/I2C_Temp_Sensor_and_GPS_Sample

http://www.wsnak.com/wsnakblog/?p=323

http://www.wsnak.com/wsnakblog/?p=409

改めて作成したサンプルはここに．

//リンク

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

これでデータが取得できました．

### LoRaWANサーバへセンサデータを送る＆確認する

取得したデータを送るようにします．これも他のサンプルとかを見て適当に．

作成したプログラムはこちら．

//リンク

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

あとの視覚化のために送信時のデータフォーマットをちょっといじって10進数にしています（プログラムをみてね）．

Senseway Mission Connectのデバイスの通信量が増えていることを確認します．

しかしこれだとセンサのデータの中身を見ることができません．

### Azureの利用 - 可視化を試す

Azure IoT Hubを利用して可視化サービスであるPower BIを試します（ぐぐったらでてきたので）．アーキテクチャは以下の通りです．

//画像

Azure IoT Hubとの連携ができるとのことで，センスウェイさんの公式サイトのマニュアルを参考に設定とかを行います．

https://service.senseway.net/manual/manual-with-azure-iot-hub/

Azureのアカウントは既に持っていたので，そのままAzure PortalからIoT HubとSenseway Mission Connectを連携設定しました．

連携できると以下のメッセージを確認できます（うまくいかないとErrorを吐きます）．

```
項目	値
外部連携設定	Azure IoT-Hub
外部連携最終結果	2019/01/30T01:32 OK
```



こちらのサイトを参考にAzure IoT Hub→Analytics Jobs→Power BIを繋ぎます（Power BIはマイクロソフトが提供する強力な可視化サービスの一つです）．Power BIは今回オンラインの３０日無料体験版を利用しました（メールアドレスは所属団体のものを利用しないといけないので大学のメールアドレスを）．

https://docs.microsoft.com/ja-jp/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi

うまく連携できると，Analytics JobsのMonitoringで以下のようにデータが届いていることを確認できます．寝る前にうまく言って，起きてからノードを電源につないで大学にでかけたので，それ以降データが流れてきていることもわかります．

![AzureIoTHubMetricsCheck](.\img\AzureIoTHubMetricsCheck.PNG)

参考：https://docs.microsoft.com/en-us/azure/iot-hub/tutorial-connectivity



とりあえず，マイクロソフトの公式リンクの通りに進めると，Power BIでこんな感じに見えました．なお，Azure Stream Analytics JobsをStopするとPower BIにデータは送られなくなります．

![PowerBI_0](.\img\PowerBI_0.PNG)

このままだとデータが届くのは確認できますが，肝心の温度データが取れていないので，ちょっと試行錯誤．何もしていない状態だと，JSONの各Elementが取れていますが，これ何もできないです．

ここで取れていたフィールドはSenseway Mission Connect→Azure IoT Hubで流れてきたものです．そこでこの中にデータが入っているはずなので，その中身を取り出します．

Azure IoT HubにはSenseWay Mission ConnectからなにかしたのFormatで送られているはずです．以下を参考にデータをParseするQueryをAzure IoT HubのQueryに書きます．

https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-parsing-json

JSON Formatはセンスウェイさんが公開していないようだったので，Power BIに最初見えていたElementを元に，適当に当たりをつけて調べました（スターターキットに含まれていた資料のとあるページも合わせて参考にしました）．どうやら主なデータは"mod"に入っているようでした．また複数のgwが受信することも想定しているようですが，JSON Arrayは対応していない？ようなので取れなかったです．

AzureのJop topologyに以下のクエリを書きます（#のコメント部分は消してください）．

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

Azure IoT Hubが付与するJSONはこちらを参考：https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-define-inputs

Azure Stream Analytics上のSQL Data Types：https://docs.microsoft.com/en-us/stream-analytics-query/data-types-azure-stream-analytics

SQLクエリ中の型変換についての参考Q&A：https://stackoverflow.com/questions/45550481/azure-stream-analytics-sql-to-convert-string-to-float

SELECT文はデフォルトだと「*」と，とりあえずJSONの中身を全て取ってくる仕様だったので，Senseway Mission Connectから取れそうなものを引っ張りました．周波数やメッセージカウントが取得できそうです．なお，そのままだとmod.dataはStringなので，公式のマニュアルに従ってbigint型へCASTします．

一度Stream Analytics Jobsを停止後，Queryを書き換えて再度Startします．するとPower BIのフィールドにこんな感じに見えると思うので，とりあえずそれぞれのデータをリスト表示してみました．

![PowerBI_2](.\img\PowerBI_2.PNG)

Queryで指定したElementがきちんとロードされており，とりあえずレポートでそれぞれのデータを見ることができました（固定の周波数じゃないのが気になります）．

肝心の温度データは”temperature"に格納されています．ここで，Arduinoのプログラムは10進数でそのまま送るように（20.32度→2032として送信）変更しており，mod.dataの中身は10進数の温度データのみとしています．なので，bigint型にCASTしてそのままPower BIに流し込むことで，すぐに折れ線グラフにすることができるはず！

折れ線グラフを選択し，X軸にenqueuedtimeを，値にtemperatureを選択し，折れ線グラフにした結果はこちら．

![PowerBI_3](.\img\PowerBI_3.PNG)

スケールとかは，ローラーのようなアイコンからいろいろいじれます．なお，Y軸はそのままなので，気温は×100した値になっていますがご愛嬌ということで．これで私の部屋の気温を無事グラフにすることができました．途中でばーんと上昇しているのは私がセンサを握ったからです．物理でバッグです．

これで実機を利用してセンサ値を取得，可視化することができました！！！おつかれさまでした

本当は公開したかったのですが，その機能はPro（めっちゃ高い）だけらしいです．ざんねん．

## （おまけ）KashiwaGeeksを少し改造

KashiwaGeeksにおけるTASKの実行頻度は"interval by minute"とあるように分単位でしか設定できません（利用用途としては十分です）．

ライブラリのApplication.cppにあるaddTask()では引数の型がuint32_tなので小数点も無理です（◞‸◟）

デバッグがしづらいですね．というわけでちょっと改造しました．

forkして自分のリポジトリにおいています．

//ここにGithubのリンク

Application.hの188行目：floatへ

Application.cppの160行目，563行目，109行目：Floatへ

これで

```
TASK(taskTemp, 0, 0.1),
```

とすると，0.1分（＝6秒）間隔で実行できるはずです．ただし，デューティサイクルには気をつけてください．電波を扱う場合はお互いにマナーを守りましょう．

※なんか４回目くらいで停止するのでライブラリ側で何かしら制限されているかもしれません

## 感想

修論があって，一息ついてから初めたのでちょっと遅くなってしまいました．キットを送っていただいたのに申し訳なかったです．

今回最も大変だったのは．．

- KashiwaGeeksの理解
- Senseway Mission ConnectのJSONフォーマット周辺（仕様を公開したほうがいいと思います！）

- Power BIでグラフを表示するためのJSONの変換周辺

- Online Power BIについての情報が少なすぎる
  - Desktop版の資料が豊富です．．

ちょうど２週間位前からAzureを触り始めていたので，ちょうどいいタイミングでした．Azure IoT Hubは初体験だったので，他のところでも活かせそうです．



