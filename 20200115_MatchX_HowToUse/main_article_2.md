## MatchX Gatewayの紹介 - Get Started

今回は「MatchX」というLoRaWANサービスを提供している企業の「MatchX」という企業のLoRaWANゲートウェイを紹介します。

https://www.matchx.io/

![Title](/Volumes/TRANSCEND/users/blog/20200115_MatchX_HowToUse/Title.png)



そもそも、MatchXはドイツの会社なのですが、どうやらLoRaWAN関係の情報を検索している中で、日本で情報を発信しているLoRaWAN関係の人として見つかったのが私だったようで、Slackなどを介して連絡を頂きました。

実際に日本へ何度か足を運んでいるようで、実際に顔を合わせた際に今回紹介するゲートウェイを頂いたので紹介します。

//目次

//書くこと

・そもそもMatchXというのはどういう製品か、について

​	BlockChain？についてなど、簡単に説明

​	セキュリティについても記載などがあれば合わせて追加説明を行う

・QuickInstallationについてのガイド

・次回以降についてはこのGatewayについての使い方について



MatchXの使い方について解説する



### MatchX Gatewayについて

製品のページはこちらになります。

https://www.matchx.io/product/matchx-gateway/

価格は400ユーロドルなので、5万円弱といったところでしょうか。

#### 1. 技適について

2019年末、総務省は「技適身的取得機器を用いた実験などの特例制度」を発表しました。

日本ではLoRaWANは920MHz帯を利用しています。こうした通信時に電波を発する機器は原則「技適」の取得が必要なことは皆さんご存知だと思います。

総務省が発表した特例制度は、技適を取得していなくても短期間の実験などを目的とした場合は、申請することで利用可能となるというものでした。

今回MatchXのGatewayは、日本の技適（Japanese Certification）を取得しているため、デモとして動作確認はもちろん、販売も可能となっています。

#### 2. MatchX Gateway開封

MatchX Gatewayには、電源用アダプタ、アンテナ２本、本体、固定用具の４つが同梱されています。



![MatchX_1](/Volumes/TRANSCEND/users/blog/20200115_MatchX_HowToUse/MatchX_1.jpg)



なお、１枚の紙のガイドだけ同封されていたため、適当にWEBサイトを参考にした。利用方法などについては以下を参考にする。

https://www.matchx.io/getstarted/

また、Gateway本体に搭載されている入出力ポートはこちら。

![MatchX_2](/Volumes/TRANSCEND/users/blog/20200115_MatchX_HowToUse/MatchX_2.jpg)



* USB TypeC
  * デバッグ用とのこと
* PoEポート
  * 電源入力兼有線でのネットワーク接続
* USB2.0
  * 外部記憶領域へのアクセス



なおPoE経由で接続するとこのようになります。大体最近のLANケーブルはPoEに対応しているはずなので問題なく動作すると思います。



![MatchX_3](/Volumes/TRANSCEND/users/blog/20200115_MatchX_HowToUse/MatchX_3.jpg)



#### 3. MatchX Gateway 特徴とスペック



気になるGatewayのスペックについては製品ページより抜粋します。



**Description**



LBT(Listen Before Talk)技術が採用されているため、データの衝突を避ける事ができるため、データ送信の信頼性を向上させることができます。



※LBTはキャリアセンス技術であり、日本では周波数によってキャリアセンスに必要な時間は異なるものの、必須な技術です。具体的にはARIB STD-T108に規定されています。電波出力制限なども規定されています。



**特徴**



* Securely Encrypted
  * End to End（端末からLoRaWANサーバ）までを暗号化することによりデータのセキュリティを担保します。MatchX Gatewayはデータ自体を解読できません。
* Increased Efficiency
  * 衝突なしで35kmのデータ送信を達成（日本では出力的に難しいと思われる）
  * これはLBT技術（キャリアセンス）によるもの
* Weather Resistant Outdoor Gateway
  * 外部環境に強い筐体
* Free Cloud Access
  * 製品を購入すればLoRaWANサーバを無料で利用可能とのこと
* In-house firmware and hardware engineering
  * ファームウェアとハードウェアエンジニアリング





**スペック**



* OS
  * OpenWrt/LEDE
* プロトコル
  * LoRaWAN v1.02
* LoRaモジュール
  * SX1301搭載
  * 電波出力：30dBm（日本では通常13dBmまで）
  * 感度：-143dBm
  * 通信範囲：20km（最大出力の場合）

* 周波数
  * AS923（日本）対応
* 入力
  * USB 2.0, PoE Port(Ethernet)/10/100 Mbit LAN
* デバッグインタフェース
  * USB Type C for UART, 4 GPIO pins
* その他
  * GPSアンテナ搭載
  * WIFIアンテナ内部搭載
  * 8 GPIO pins
  * 保護等級IP65







---

以下実際のインストレーションについて

### 全体について

//写真

そもそもどういうものかについての製品紹介などについて

### Quick Installation

#### 1. 電源

MatchXの電源はPoEだけです。

PoEを出力できるアダプタが同梱されているので、これとPoE対応ケーブルをつかってゲートウェイに電源を入力しましょう。

うまくいくとLINKのLEDが点灯するはずです。これと同時にWiFiのアクセスポイント機能が有効化されるため、MatchXから始まるSSIDのWiFiが飛んでいるか確認してみましょう。



#### 2. WiFi

Users need to have a laptop or a PC that has WiFi connectivities, then after the Box is powered on, the SSID that in a format "MatchX_Box_xxxx" will be available for connection. The default password is S/N of the device. After connected, users can use their web browser to configure the wifi backhaul at 192.168.8.1 . The following picture shows the interface of WiFi connection.



http://192.168.8.1/



![Fig1](J:\users\blog\20200115_MatchX_HowToUse\Fig1.PNG)



#### 3. Ethernet接続（Internet接続）

MatchXには有線ネットワーク接続機能と無線LANによる接続機能の2種類があります。

今回はLoRaWAN Gatewayを無線LAN経由でインターネットに接続できるようにするため、「WiFi Client Setting」のAPリストからWiFiに接続します。おそらくPoEの電源にあるEthernetポートに有線で接続することも可能です。なお、Gatewayが接続できる周波数は2.4GHzのみのようでした。

![Fig2](J:\users\blog\20200115_MatchX_HowToUse\Fig2.PNG)

このようにConnected to APになれば、現在接続されているAPから割り当てられているIPアドレスが表示されます。



#### 4. LoRaWANサーバの設定

