# IoTSecJP Tokyo #5へ参加してきました

今年の3月16日、昨年９月ぶりに開催されたIoTSecJP Tokyo #5へ参加してきました．

http://ruffnex.net/iotsecjp/

https://iotsecjp.connpass.com/event/120450/

https://iotsecjp.connpass.com/event/120450/presentation/

いつにもましてレベルが高く，貴重なお話を聞くことが出来ました．せっかくなのでメモを整理して参加記として残して置きます．



今回会場は**株式会社ラック様**のオフィスでした．ありがとうございます！

さて，今回の発表スケジュールは以下のとおりでした．

```
IoTSecJP東京 Version5.0

時刻	タイトル	時間	その他
12:30	開場	30[min]	IoTSecJP運営
13:00	会場書注意など	5[min]	IoTSecJP運営( @r00tapple )
13:05	「{登壇内容調整中}」	30[min]	kidasan ( @kidasan)
13:35	「{登壇内容調整中}」	30[min]	NV ( @nvsofts)
14:05	「Hardware Trojan できるかな」	30[min]	omoikane ( @jm6xxu)
14:35	休憩	10[min]	
14:45	「ドローン コレクション 2019年 春」	15[min]	hogehuga ( @hogehuga)
14:50	「IoT Securityコンサルトしてハードウェア設計/実機診断をやったときのリアルな事例を紹介してみますよ」	30[min]	kawashin1 ( @kawashin1)
15:20	[LTEデバイス攻略までの3ステップ]	30[min]	RyskUmts(@tansokun920)
15:50	[安全性の高いプログラミング言語でのIoTデバイス開発について]	30[min]	LDScell(@LDScell)
16:20	[Wi-Fiのチャネルベース中間者攻撃とKRACKsの話]	30[min]	Nao(@nsec_life)
16:50	[電力線通信に対するDoS攻撃の検討]	30[min]	Hidekazu Asaba(@Belboz16)
17:20	「4Kカメラをハッキングしてインスタ映えしてみた」	30[min]	黒林檎 ( @r00tapple )
17:50頃	終わりに	5[min]	IoTSecJP運営 ( @r00tapple )
18:00	撤収		参加各位
```

//目次

メモベースで記録を残しておきます．

## 1. Kidasan「相関電力解析に期待するもの」

CANのリアルタイム性を維持しつつ不正ECUを特定できるやつをSCIS2018で発表していたそうです．

*相関電力解析とは？*

> 相関電力解析攻撃（CPA, Correlation Power Analysis）とは、暗号回路の動作時の消費電力波形を複数観測し、それらを統計的に処理することで秘密鍵を求める攻撃手法です。統計処理には相関係数というある2つの値の関係の強さを示す数値を用います。

https://www.lac.co.jp/lacwatch/people/20180601_001617.html

せっかくなのでラックさんのサイトから引用しました．

今回kidasanは，上記引用にある「秘密鍵を求める攻撃手法」としてではなく，対象機器がどういった処理をしているかを求めるようなリバースエンジニアリングとしての解析を指していました．

**□相関電力解析に期待するもの**

「サイドチャネル攻撃と呼ぶと範囲が広すぎる，これをもっと狭く呼びたい」そうです．

しかし，いまのコンピュータは一般的にマルチコアになっている→「非同期になっているので相関電力解析では読むのは非常に難しい」

この仕組を利用してCANバス上におけるTCU経由で汚染された不正ECUを検知することも可能であるかもしれないそうです．

**□実際に試してみる**

仮定「プロセスが動いおているのだから何らかの波形が見れるはず」

結果：**実際に観測しても解析は難しい**　とのこと

目立つのはWiFiのScanくらい？プログラムの解析までは非常に困難

4コアで動いているので，どのプログラムがどこで走っているのかも難しい

**□解析してわかること(わかったこと)**

スケジューリングする際には電力が低下する（振幅が下がる？）

**□やろうとしていること**

波形の一つ一つをタスクに紐付け，何が見えるか検証してみる

**デバイスの電源にAddするだけのエンドポイントセキュリティ**が最終的な目標

## 2. Omoikaneさん「Hardware Trojan できるかな」

NVさん「ちょっとSurfaceが死んだので」

Omoikaneさんが2番目に登壇されました．昨年から何かと話題なHardware Trojanについて，実際に自分で試すことができるかやってみようとの計画です．

**「Hardware Trojanしたい」**

*Hardware Trojanとは*

> A **Hardware Trojan** (HT) is a malicious modification of the circuitry of an integrated circuit. A **hardware Trojan** is completely characterized by its physical representation and its behavior. The payload of an HT is the entire activity that the **Trojan** executes when it is triggered.
>
> (tokina訳) Hardware Trojanは統合回路の回路レベルに悪意のある変更を加えたものです．また，その物理的な見た目や振る舞いなどから完全に特徴づけることができます．Hardware Trojanが意味する対象は「実行された全ての動作」です．

結論：**できませんでした（できたら会社のブログに書きます）**

年度末ということで，お忙しいとのことです．（それはそう）

**□こんなの話題になりませんでした？**

Bloomberg Businessweekより

https://www.bloomberg.com/news/features/2018-10-04/the-big-hack-how-china-used-a-tiny-chip-to-infiltrate-america-s-top-companies

小さいマイクロチップが中国にデータを送っているらしいといううさが昨年ひっそりと囁かれていましたが，実際には本当にHardware Trojanなのかは判明していないまま今に至ります．

またファーウェイの端末に不要な部品がみつかったらしいですが，それも結局は真相は不明なまま・・・．

最近はアメリカを初め，日本でもファーウェイ排除の動きが見られますが，どうなるんでしょうか・・・．

**□今回のLTでのお話**

今回は文献調査とのことで、文献からHardware Trojan Warの紹介いただきました。

https://www.amazon.co.jp/Hardware-Trojan-War-Attacks-Defenses-ebook/dp/B078C83MMV

> ハードウェアトロイとは特定の目的を達成するために回路の動作を変更するように設計された，回路の意図的かつ悪意のある改変

- NSA Toolkit
  - ツールのリンクがわからなかったですが、Ghidraを開発しているNSAだと思われます
- SOLDERPEEK
  - https://github.com/securelyfitz/solderpeek
- SLOTSCREAMER
  - コンバータを利用してメモリを改変
- Bad USBーWiFi搭載
  - 初期からTrojanが入っているタイプ

**□簡単にまとめ**

- PoCレベルはたくさんある。しかし，実際に存在しているかは現状不明
- 入っていても正常な動作を実現しないといけないので，わざわざハードウェアとして実現する意味は．．
  - ​	ソフトウェアで実現したほうが楽なのでは・・・？
- 最初から入っているバックドアの検知は非常に難しい
  - サプライヤーレベルでセキュリティの観点を入れるといいのでは？
    - →実際に信頼できるサプライヤーのリストとかはない？​
    - →ないらしい

## 3. NVさん「eMMCの話」

https://t.co/fZCyuReIvF

eMMCをいろいろする話を、ということでNVさんから「eMMCの吸い出し」をメインに聞くことが出来ました。

**eMMCについて**

- eMMC: embedded Multi Media Card 

- 最高400MB/sの転送速度、NANDフラッシュ処理が不要などの特徴を持つ

- パーティション: ブート１，ブート２，ユーザデータ，RPMB (ユーザーデータは普段ユーザーがデータを格納するところ)

- pinについて
  - Vcc電源，VccQ，
  - Vss, VssW: GND,
  - CLK
  - CMD command io
- eMMCの吸出しツール
  - ROMライターRT809H
  - JTAG Box: Easy JTAG
  - 一部のSDカードリーダ
    - TranscendのTS-RDF5Kが使える（ユーザデータ領域しか読めない
  - E-MATE Xがいい感じに読める：１３種類に対応している
    - https://ja.aliexpress.com/item/E-mate-box-Emate-pro-box-E-Socket-EMMC-TOOL-all-in-1-support-BGA-153/32775584107.html

- E-MATE Xの使い方が参考になる動画がYoutubeに（YoutubeでE-MATE Xと検索すればOK！）

- EXEなど専用の解析ソフトはVMで実行したほうがいい（怪しいので）

## 4. hogehugaさん「ドローンコレクション 2019年 春」

「最近のドローン」について紹介していただきました

https://speakerdeck.com/hogehuga/iotsecjp-tokyo-number-5-dronecollection2019

水中ドローン、酒気帯び運転問題＠ドローン

ドローンの事故率，故障率（少なくとも公開されていない？）、これらが必要になってくるだろうとのこと。

DJIのTelloがおすすめ，JSONでやりとりしているらしい？

https://t.co/vRBi9qYLYU



> ドローンの話の補足情報(1/2)
> 米国では登録義務対象になる最低重量について、記載のようなリスク評価をして 250g と決定した。
> 落下時のエネルギーと致死率、飛行時間等などを総合的に勘案し、許容できると考えられるものとしている。

https://twitter.com/hogehuga/status/1106811551233667073

> ドローンの話の補足情報(2/2)
> 下記資料の6Pごろからドローンの事故報告の記載がある。2015/12以降、飛行申請して事故を起こしたら報告が必要。NEDO 国立研究開発法人新エネルギー・産業技術総合開発機構 でそこそこ情報がありそう。

https://twitter.com/hogehuga/status/1106813581675229184

## 5. kawashin1さん「IoT Securityコンサルトしてハードウェア設計/実機診断をやったときのリアルな事例を紹介してみますよ」

某社でCloud Security ＆ IoT Securityを担当しているkawashin1さんからコンサル（営業？）の面から、結構がっつりと実例を通してお話を聞くことができました。

https://www.slideshare.net/ShinichiroKawano/2019-0316-iotsecjpslideshare

- 今回は情報収集と勉強できた人向けのLT

- Fセキュアについてざっと
  - 600人くらいはコンサルティング業務に従事
  -  ヨーロッパでCyber Crimeがあった場合に調査コンサルティング業務を行っているのがメインの会社（具体的に何件なのか不明，事実老舗の会社）
  - １４年以上のIoTコンサルティング業務経験
- F-Secure 「車載IoT機器　セキュリティ診断サービス」
  - 車載IoT以外にも，機内IoT，航空管制システムなど

**□カーナビの攻撃テストについて**

- システムファームウェア抜出し，入れ替え可能か

- CANBUS用のData Diode，他のシステムが，，

- CAN BUSの分離が完璧か，ファームウェアアップデートの仕組みに脆弱性はないか，侵入できるかペネトレしてみる→さらにそれらに基づいて対策事例をアウトプットする？？？？

- 具体的な攻撃手法は口外できない部分とのこと

https://andrea.io

にてFセキュアが発見した資料などを公開しているらしいので，是非活用ください？

**□Q&Aがほしい！**

- ロゴがいつから赤になりましたね？
  - ２０１７年にロゴが赤くなりました

- 車関係 - 日本とヨーロッパで意識の違いは何がある？
  - ヨーロッパでは全体のテストのお願いがあった，日本ではやらなければいけないので探しています
  - 最近問い合わせが着ている気がします
  - アメリカもやっている（何社か口外は禁止とのこと，５−６年，テスラは？）
- ウェアラブルデバイスについてのペネトレもあったらしい
- 日本にもペネトレの人がいる？クラウドと飛行機を攻撃できる人がいるとかフィンランド人が強い（人が来る）

ライバル意識をもつより，一緒に良くしていきましょうとのこと

[youtu.be/0_ZfuMlNJk8](https://t.co/PtvT1RGZjz)

F-SecureさんのCTFレッドチームの動画です

## 6. Uematuさん「LTEデバイス攻略までの3ステップ」

LTE通信機器の診断に関してお話いただけました。あまり多くメモが取れなかったので、残っているものを整理して一部を抜粋して紹介します

- ”LTEFuzz”と名付けられたツールを用いて検証→認証バイパスできる脆弱性が発見されたらしい
  - https://sites.google.com/view/ltefuzz
- アタッチ，？，ハンドオーバ
  - アタッチの認証シーケンスをバイパスできる脆弱性があった場合には？
  - これはLoRaWANのアクティベーションでも同様のことが言える？

**□どうやってLTE通信を行うIoT機器を偽装するのか？**

- 中身がわかっているSIMカードがないと偽装できない
- IoTデバイスで使用されるSIMは組み込みSIM（eSIM）（つまり差し替えなどが考慮されていない）
- eSIMとSIMの違いは形状のみ

つまり、組み込まれているSIMを無理やり取り出して、自前のSIMに差し替えればいいのでは？

→実機で試したところ，**実際にeSIMでむりやり普通のSIMに差し替えてみた**ら普通に出来たとのこと（これでテストSIMを指して脆弱性診断もできそう）

**□参考になる書籍や論文ツールなど**

- 役に立ちそうな書籍
  - 携帯電話はなぜつながるのか？
  - LTEの教科書
  - その他標準ドキュメント・・・

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">LTEの独学用本は以下がおすすめ。 <a href="https://twitter.com/hashtag/IoTSecJP?src=hash&amp;ref_src=twsrc%5Etfw">#IoTSecJP</a><br>■初級編<br>携帯電話はなぜつながるのか 第2版   中嶋信生 <a href="https://t.co/hzOF1H4ZEh">https://t.co/hzOF1H4ZEh</a> <br>■中級編<br>インプレス標準教科書シリーズ 5G教科書 ―LTE/ IoTから5Gまで―   服部 武 <a href="https://t.co/IGRqJF0QvG">https://t.co/IGRqJF0QvG</a></p>&mdash; RyskUmts (@tansokun920) <a href="https://twitter.com/tansokun920/status/1106817578456907776?ref_src=twsrc%5Etfw">2019年3月16日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

- OpenAir Interfaceというものもあるらしい

  - https://www.openairinterface.org

  - > The OpenAirInterfaceTM Software Alliance (OSA) is a non-profit consortium fostering a community of industrial as well as research contributors for open source software and hardware development for the core network (EPC), access network and user equipment (EUTRAN) of 3GPP cellular networks. 

- LTE　Securityの論文

- OpenLTEとSRS LTEは？SRS LTEのほうが手が入れやすい（通信事業単位のノード単位でソースコードが書かれているらしい）

  - https://www.softwareradiosystems.com/tag/srslte/
  - https://github.com/srsLTE/srsLTE

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr"><a href="https://twitter.com/hashtag/IoTSecJP?src=hash&amp;ref_src=twsrc%5Etfw">#IoTSecJP</a> で質問されていた内容を少し整理しました。セルラーOSSについてのまとめ。<br>SDRで使えるセルラーOSS <a href="https://t.co/3snPtMKUTb">https://t.co/3snPtMKUTb</a></p>&mdash; RyskUmts (@tansokun920) <a href="https://twitter.com/tansokun920/status/1107638216192987136?ref_src=twsrc%5Etfw">2019年3月18日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## 7. LDScellさん「安全性の高いプログラミング言語でのIoTデバイス開発について」

「セキュアなプログラミングするためには？　Rustはどうか？」から始まりました。

- 様々な安全機能
  - メモリ安全
  - バッファオーバーフロー/バッファオーバーラン
  - Nullポインタ参照．．．

- 以前話題になったHeartBleed攻撃
  - Point：メモリ操作の実装バグ（バッファオーバーラン）

- 情報漏えい＜サービス停止（プロセスクラッシュ）
  - コンパイルエラーになる（これが一番理想）

- 組み込みではC/C++でするしかないのでは？
  - IoTデバイス開発でもプログラミング言語に制限がなくなりつつある
- LLVM -  GCCに匹敵するコンパイラ実装の敷居がさがった
  - LLVMで解釈できる中間言語を出力できる言語でればいい→自作できる
  - (実は)Go，Rustも現在対応している
    - Tiny Goなるものがあるらしい
    - <https://github.com/tinygo-org/tinygo>

Rust普及の課題として「Rustは簡単にかける言語ではない（難しい）」が挙げられるとのこと。そのかわり以下の利点があるそうです。

- メモリ管理をコンパイラがかなり検証

- その他方安全やメモリ検証などまでサポート

- →コンパイラが厳しい！
  - Rustではめちゃくちゃに厳しいコンパイルレベルがあるらしい
- 言語仕様特徴
  - 所有権
  - 借用
  - ライフタイム

「RustではHeart Bleedは発生しなかったのではないのか？」

- 所有権の例：アドレスを参照して他の変数から他の変数の値を変更することが出来ない
  - 所有権は一つのみ，もし所有権違反をした場合にはコンパイルエラーとなる
  - これは動的配列でも同様

以下参考になりそうな情報

- Rustの組み込みの本を和訳したものが公開されているそうです
  - Emmbedded Rust Book (和訳済み)
  - https://tomoyuki-nakabayashi.github.io/book/
- Discovery （和訳中）
- IPv6 TCPエコーサーバ
  - C言語上のRTOS（Zephyr）上で動くアプリケーションをRustで実装
  - C言語で既存で動く部分の上でRustで開発できれば

これからの課題として・・・

- C言語とのギャップが大きく，学習コストが高い

- C/C++の資産が大きい，これから増やしていく必要があり

- 新しい言語「ZEN」
  - **connectFree社**開発（2015~）
  - Reduce your stress

## 8. Naoさん「Wi-Fiのチャネルベース中間者攻撃とKRACKsの話」

ラックでセキュリティエンジニアをしているNaoさんが、challen-based MitM Attacksを実際に試した報告をしてくれました。

https://speakerdeck.com/nsec_life/channel-based-mitm-attacks-using-csas

CSAを利用したチャネルベース中間者攻撃の論文発表が2018年よりvanhoef氏らによって発表されたとのこと

- Google Scholarで検索したらみつかりました
  - https://dl.acm.org/citation.cfm?id=3212493

- 日本でも同攻撃の実現可能性
  - https://mrncciew.com/2014/10/29/cwap-channel-switch-announcement/

**□実際に攻撃してみた / channel-based MitM Attacks**

- 資料
  - krachattacks-poc-zerokey（PoCのツールです）
    - https://github.com/vanhoefm/krackattacks-poc-zerokey
  - KRACKs demo tool
- KRACKの脆弱性があればWPA2の復号ができる
  - 今までのIpアドレスが変わっている→ニセのAPにつながっている？
  - 先と同様にネットにはつながる，しかし中間者攻撃が成功している
- WPA2の通信が復号されている→KRACKの脆弱性
  - 再接続要求をビーコンを出して強制的にAPを変更する

**□対策は？**

- 通信が暗号化された状態のみチャネル情報を交換する

- クライアント側でCSAを無効化
- APのチャネルを記録しておき、普段と異なる場合に通知

とりあえず・・・

- HTTPやVPNによる暗号化
- KRACKsの修正パッチ
- チャネルベース中間者攻撃を利用した**DoS攻撃は防ぐことができない**

2.4GHz帯でも攻撃が成功してしまう（5GHzでも関係ない（クライアントが対応していると結局同様））

攻撃に必要な情報はSSIDのみ

**低レイヤパケット解析は楽しい！**

<blockquote class="twitter-tweet" data-partner="tweetdeck"><p lang="ja" dir="ltr"><a href="https://twitter.com/hashtag/IotSecJP?src=hash&amp;ref_src=twsrc%5Etfw">#IotSecJP</a> で<a href="https://twitter.com/nsec_life?ref_src=twsrc%5Etfw">@nsec_life</a> さんから発表があったCSAsを使用したWifiのMitMに挑戦している。<br>Wifi Hackingを試したのが半年以上前なので環境など全てを忘れていたが当時の自分のblog（<a href="https://t.co/VzoLjB2bOj">https://t.co/VzoLjB2bOj</a>）を読んで思い出してきた。半年前の自分を褒めてあげたい。</p>&mdash; graneed (@graneed111) <a href="https://twitter.com/graneed111/status/1107277438646546432?ref_src=twsrc%5Etfw">March 17, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## 9. Hidekazu Asabaさん「電力線通信に対するDoS攻撃の検討」

電力線通信に対するDoS攻撃についてのお話をお伺いしました。

https://speakerdeck.com/belboz/examination-of-dos-attack-via-power-line-communication

- 「電力線」と聞いて私はピンとこなかったのですが、これはPLCのことですね。

- > PLC（電力線通信）とは、Power Line Communications の略で、電気エネルギーを供給する電力線に高周波の通信用信号を重畳して伝送させることにより、電力線を通信ケーブルとしても使用する技術のことです。
  >
  > https://www.m-system.co.jp/mstoday/backnum/2007/03/iandn2/index.html

「**消防法**」がキーワードですね。

> 我が国が長年蓄積してきた防災知識そのものである

** □やってみた**

- 火災の発生を検知、報告するのは「火災受信機」
  - これに対して攻撃できれば影響が大きいのでは？
- しかし「バレない攻撃は難しい」

- 火災検知ビットをなりすましてみる
  - プロトコル上の火災検知データい、電圧（ビット）を印加して火災をご認知させる

(詳細な方法などについては公開しないこととします)

先月四国電力で再現性の解説がされている（愛媛新聞）そうです

## 10. 黒林檎さん「4Kカメラをハッキングしてインスタ映えしてみた」

「**プリンタからドメインコントローラへペンテストしてみました**」

- 役に立つサイト！
  - https://iot-security.wiki/
  - https://codeby.net/

- Orange PiとLime SDRで基地局を偽装するデモ
  - 解析してバックドアを開いてみる→自分で操作できますよね
  - ただ，そこら辺ハックしても保証してませんよ
- 脆弱性の開示→IPAへ報告→**バックドア消えました**

**□分析フェーズ**

- 1.ハードウェア分析

- 2.ファームウェア分析

- 3.無線プロトコル分析

- 4.モバイルアプリケーション

- 5.webアプリケーション

- 6.クラウドサービス

推測による指摘や公開は控えることにするそうです（とくに最近そういうの危険な流れが・・・）

**脆弱性を見つけたらまずは届け出ることが先！**

## 最後に



https://twitter.com/r00tapple/status/1107563917713113088

> 今回の #IoTSecJP の知見は２つ
>
> ・sli.do を使用したら質問が増える。(anonymousで質問可能)
> ・HDMIの出力ができないけど、参加者がHDMI出力Onlyだったら appear.in 経由で登壇者の画面を共有して出力することが可能。(※これは事前に運営が調べて報告しろ感)

だそうです．sli.doというサービスは初めて知りました．SlackやTwitterを触っていない人でも質疑を匿名でできるので心理的障壁を減らしてくれますし，いい方法だなと思いました．

また最近，ちょっとしたことをきっかけに逮捕者が発生しており，界隈でもCTF writeupをクローズしたり，CTFサーバが停止したりととても良くない動きが見られます．Conhive事件が無罪になる、一般社団法人が寄付を募るなど、こうした動きが活発化し、その結果がどうにか良い方向に向かっている気がします。先行きは暗いですが・・・。

今後はセキュリティリテラシに注意することはもちろんですが，こうした技術がホワイトハットに基づいていることもしっかりと周知していく必要があると思います．ということで今回のこの記事もそういう意味を込めて公開したいと思っていました。

次回のIoTSecJPで登壇できることを期待して・・・

※紆余曲折あり（というか基本的に空いた時間やタスクの合間にすすめており）遅くなってしまったことご容赦ください

※不備、失言等ありましたらコメントやDMなどでお願い致します

## おまけ

https://iotsecjp.booth.pm/items/1302056

技術書典6にてIoTSecJP Vol.3の頒布が行われました（BOOTHでの販売は期間限定だそうです）。

また、中村行宏さんの「サイバー攻撃の教科書」も販売されております！

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr"><a href="https://twitter.com/hashtag/IoTSecJP?src=hash&amp;ref_src=twsrc%5Etfw">#IoTSecJP</a> にて、圧倒的進捗を出した中村行宏氏が新しく本を出版いたしました！<br>サイバー攻撃を図解で解説していく一冊となっています！<br>ご興味がある方は、お手にとっていただけると幸いです。<br><br>・amazon商品ページ<a href="https://t.co/e7sKLWqtZC">https://t.co/e7sKLWqtZC</a><br>・書籍公式サイト<a href="https://t.co/f5LdC2cfph">https://t.co/f5LdC2cfph</a></p>&mdash; 黒林檎 (@r00tapple) <a href="https://twitter.com/r00tapple/status/1108020145388023818?ref_src=twsrc%5Etfw">2019年3月19日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

##　おまけ2

黒林檎傷害事件「〜黒林檎の部屋〜 未成年による傷害事件体験記」がBOOTHで公開されています。事件解決までの道のりが非常に役に立ちます！

https://r00tapple.booth.pm/items/1305533

@_tokina23  