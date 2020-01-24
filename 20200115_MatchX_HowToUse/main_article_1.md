## MatchX Gatewayの紹介 - Get Started

今回は「MatchX」というLoRaWANサービスを提供している企業の「MatchX」という企業のLoRaWANゲートウェイを紹介します。

https://www.matchx.io/

![Title](https://raw.githubusercontent.com/himitu23/blog_article/master/20200115_MatchX_HowToUse/Title.png)



そもそも、MatchXはドイツの会社なのですが、どうやらLoRaWAN関係の情報を検索している中で、日本で情報を発信しているLoRaWAN関係の人として見つかったのが私だったようで、Slackなどを介して連絡を頂きました。

実際に日本へ何度か足を運んでいるようで、実際に顔を合わせた際に今回紹介するゲートウェイを頂いたので紹介します。



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



![MatchX_1](https://raw.githubusercontent.com/himitu23/blog_article/master/20200115_MatchX_HowToUse/MatchX_1.jpg)



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



![MatchX_3](https://raw.githubusercontent.com/himitu23/blog_article/master/20200115_MatchX_HowToUse/MatchX_3.jpg)



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
  
    

#### 4. 参考情報



そもそもLoRaWANってなに？って人向けにはこちらをご紹介します。結構前にアールエスコンポーネンツでアルバイトをしていた際に書いた記事です。

https://www.rs-online.com/designspark/getting-start-the-lora-iot-starter-kit-with-the-things-network-part1-jp



またMatchX Gatewayについて詳しく知りたい方はこちらの開封の動画が参考になると思います。

<iframe width="560" height="315" src="https://www.youtube.com/embed/vcmqGfI0pSA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>



また、一応公式のTwitterアカウントも存在しています。



https://twitter.com/matchx_iot



### 最後に

現在、Gatewayを実際に動作確認して試しています。

動作確認と実際にどんな事ができるのかについても記事にまとめて更新したいですね。