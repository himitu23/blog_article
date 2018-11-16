# Write Up - Car Hacking Challenge @ CODE BLUE 2018

![](https://raw.githubusercontent.com/himitu23/blog_article/master/20181105_CarHackCTF_xINTCTF_Writeup/img/title.PNG)

今年も頑張ってWrite up書くぞ！

ということで，今年もCODE BLUEとAVTokyoを楽しんできました．といっても今年で2回目です．

https://codeblue.jp/2018/contests/detail_04/

今年チャレンジを提供してくださったのはイエラエセキュリティさんと，Karambaセキュリティさん，SOMPO CYBER SECURITYさんです．

https://ierae.co.jp/

https://karambasecurity.com/

Black HATで意気投合し，急遽チャレンジを行うことになったとか．

今回，私たちのチームは3位となり，表彰していただくことができました．

![](https://raw.githubusercontent.com/himitu23/blog_article/master/20181105_CarHackCTF_xINTCTF_Writeup/img/IMG_4839.JPG)

ちなみにチーム名は黒林檎さんをリスペクトして「りんごちゃん」でいきました．

ありがとうございます！(？)

//目次

## Writeup

![](https://raw.githubusercontent.com/himitu23/blog_article/master/20181105_CarHackCTF_xINTCTF_Writeup/img/IMG_4774.JPG)

今回は環境を与えられ，特に説明などはなく競技が始まりました．

「自動車を制御できたらOK」

でした．運営の方に乗っ取り成功を証明すればOKです．

昨年とは違い，今年は「ネットワークレベルだけではない」チャレンジにしたかったとおっしゃっていたとおり，Challenge３からバイナリ解析問題になり，どのチームも解けていませんでした．

つまり，各チームは２日間合計のポイントは300で打ち止めとなってしまいました．

私も競技当日はできなかったバイナリ解析をあとからいろいろ試してみたので，それについても書きたいと思います（といってもまだまだ初心者ですが）．

また，我々のチームはブレッドボードでCANを分岐させていたので，何度もCANバスを壊してしまい（バスオフ状態），「CANが壊れた札」を何度もあげていました．お手数をおかけしてごめんなさい．

また，私はあまり貢献できず，力不足を感じました・・・．

### 競技環境

競技環境は以下のような感じでした．

![](https://raw.githubusercontent.com/himitu23/blog_article/master/20181105_CarHackCTF_xINTCTF_Writeup/img/GameArch.png)

基本的に各テーブルにチームが配置され，テーブルにあるラジコンに対して攻撃をしかけます．

攻撃は前年同様CANレベルでしかできません．

またおそらく私達のチームだけブレッドボードをつかってCANを分岐させていたかもしれません．

今回，解析機としてはNVさんとそけとさんが持たれていたCANable（ほしい！）とMicroPecker，中華製の謎なアナライザ，そして用意された解析用RasPiが利用できました．

その他のテーブルではどうやらCANoeを持ってきていたチームもあった気がします．

![中央のハック対象のラジコン](https://raw.githubusercontent.com/himitu23/blog_article/master/20181105_CarHackCTF_xINTCTF_Writeup/img/IMG_4859.JPG)

![ECUを模擬するRPi](https://raw.githubusercontent.com/himitu23/blog_article/master/20181105_CarHackCTF_xINTCTF_Writeup/img/IMG_4833.JPG)

### Tutorial (50 pt.)


```
** tutorial
CAN you send messages to control the mini car?
Point:50
```

チュートリアルです．

送信にはcan-utilsのcansendを使えます．

https://github.com/linux-can/can-utils

みんな大好きcan-utils

実は私達りんごちゃんは，このチュートリアルがよくわかっておらず，チャレンジ2のあとでチュートリアルのポイントをもらいました．そのため3位になってしまいました．運営の人は何も言わず・・・

### Challenge1 (100 pt.)

```
** challenge1
File : candump-level1.log
Point: 100
```

ログファイル一つ，なるほど．

ログファイルの中身はこんな感じ．
```
(1533125865.267025) slcan0 07B#00090001
(1533125865.572777) slcan0 07B#00090001
...
(1533125867.572873) slcan0 07B#00090700
(1533125867.878477) slcan0 07B#00090700
...
```

どうやらバスにはCANID:07Bだけが流れているようです．

これは簡単で，流れているメッセージを適当にSniffし，前進後進とかをなりすますだけです．上のログファイルは実際に流れている前進，後進，右折，左折の状態が含まれています．

流れているメッセージをリバエンして，Pythonスクリプトなり，MicroPeckerなりでなりすませばOK．

### Challenge2 (150 pt.)

```
** challenge2
File: candump-level2.log
Point: 150
```

チャレンジを変更するにはコントローラをいじって現在挑んでいるチャレンジに設定します．

こちらもログファイル一つです．

ログファイルの中身はこんな感じ．

```
(1533126275.404112) slcan0 0AA#4800A0009F00
(1533126275.486353) slcan0 0AA#4900A0009F00
(1533126275.788527) slcan0 0AA#4A00A0009F00
(1533126276.090534) slcan0 0AA#4B00A0009F00
(1533126276.392959) slcan0 0AA#4C00A0009F00
(1533126276.474821) slcan0 0AA#4D00A0009F00
(1533126276.777152) slcan0 0AA#4E00A0009F00
(1533126277.079348) slcan0 0AA#4F00A0205400
(1533126277.381486) slcan0 0AA#5000A0705400
(1533126277.463680) slcan0 0AA#5100A0705400
...
(1533126296.551704) slcan0 0AA#9E00A0009F00
(1533126296.699943) slcan0 0AC#ABABABABABABABAB
(1533126296.854026) slcan0 0AA#9F00A0009F00
(1533126296.971713) slcan0 0AC#ABABABABABABABAB
(1533126297.156086) slcan0 0AA#A000A0009F00
(1533126297.180000) slcan0 0AC#ABABABABABABABAB
(1533126297.238066) slcan0 0AA#A100A0009F00
(1533126297.395621) slcan0 0AC#ABABABABABABABAB
(1533126297.540430) slcan0 0AA#A200A0009F00
(1533126297.572759) slcan0 0AC#ABABABABABABABAB
(1533126297.747856) slcan0 0AC#ABABABABABABABAB
(1533126297.842621) slcan0 0AA#A30080009F00
(1533126297.988772) slcan0 0AC#ABABABABABABABAB
(1533126298.144710) slcan0 0AA#A40080105400
(1533126298.188454) slcan0 0AC#ABABABABABABABAB
(1533126298.226842) slcan0 0AA#A50080105400
(1533126298.361315) slcan0 0AC#ABABABABABABABAB
(1533126298.529293) slcan0 0AA#A60080105400
(1533126298.831397) slcan0 0AA#A70080205400
...
```

基本的にCANID:0AAが流れているようです．実際のバスと比較すると，どうやらCANID:0ACは何かしらの注入されたメッセージだと推測されます．これから，適当なメッセージを送信してもバスには影響がないことがわかります．

また，CANID:0AAの最初の1バイト目はカウンタの役割を果たしており，さらに，後ろのバイトは動作を示しているようです．

0AA : XX 00 ?0 ?0 ?? 00

?のいずれかのビットがいずれかの状態（前進など）を示しているようです．

そのため，スクリプトで，現在流れているCANID:0AAのメッセージの最初の1バイト目を見て，それに+1し，バスをリバエンして判明した?の状態をなりすまし，バスへCANID:0AAをより高頻度で送りればOKです．

### Challenge3 (200 pt.)

```
** challenge3
File1: candump-level3.log
File2: ADC_s32k144-c3.zip
Point: 200
```

さてここからが問題です．

謎のZIPファイルと謎のログファイルがあります．結論から言うと，どのチームもこれ以降のチャレンジは解けていません．

ここで私たち（主にNVさんがやってくれ，私は後日確認してみました）がやったことについてまとめておきたいと思います．

まず何も説明がないため，このZIPがなんなのか，このZIPをどうするのか，そもそもログ解析を先にやるのか何もわかりません．

そこでまずログファイルと実際にバスに流れているメッセージを確認しました．

ログファイルの中身はこんな感じ，

```
(1533126102.822934) slcan0 0AC#529991C16E110643
(1533126103.127298) slcan0 0AC#23DA17C92B19416B
(1533126103.194215) slcan0 00A#FF2D6FF564010000
(1533126103.431857) slcan0 0AC#EEF44D14B1BFED22
(1533126103.514492) slcan0 0AC#E0EB1589F69E36E4
(1533126103.819137) slcan0 0AC#66DB30BE07ED45FE
(1533126104.123407) slcan0 0AC#79B98837D3A5CF95
(1533126104.194135) slcan0 00A#E7316FF564010000
(1533126104.428008) slcan0 0AC#07217A5B352ED3C7
...
(1533126131.011415) slcan0 0AC#ABABABABABABABAB
(1533126131.011433) slcan0 0CD#1F86ABF780DCDD3F 
(1533126131.011436) slcan0 0CD#6ACF56BB1ACABAF3
(1533126131.020957) slcan0 0AC#EFAADCE02750BB8D
(1533126131.194017) slcan0 00A#5F9B6FF564010000
(1533126131.325540) slcan0 0AC#A9BC18BE235C6669
(1533126131.408103) slcan0 0AC#38FB4F832439937C
(1533126131.643659) slcan0 0AC#ABABABABABABABAB 
(1533126131.643679) slcan0 0CD#1F86ABF780DCDD3F
(1533126131.643683) slcan0 0CD#6ACF56BB1ACABAF3
...
```

チャレンジ1と2とは異なり，どうやら\CANID:00A, 0AC, 0CDの3種類あるようです．

さらに，0AC#ABABABABABABABABはバスを観測していても流れておらず，CANID:0CDも通常は流れていませんでした．

これから，どうやら0AC#ABABABABABABABABは攻撃メッセージあり，CANID:0CDは攻撃が失敗したときに流れるメッセージであることが推測できました．また，CANID:0CDのメッセージのペイロードはいずれも同じでした．

何もわからないため，とりあえずこのログをslcan0->can0にしてcanplayerで送信してもなにも起こりませんでした．

操作のログを記録しまくり，リプレイをすると一度だけ動きましたが再現性はなく・・・．

一日目はこれ以上わかりませんでした．

#### バス上のについてANメッセージ

バス上に流れているメッセージを確認したところ，CANID:00A 1メッセージに対して，CANID:0AC 4メッセージ流れているようです．

また，適当にCANID:0ACをなりすまそうとしても全くできないことから，どうやら暗号化された通信をしているようです．

以上を踏まえ，さらになりすましメッセージを送信して検証を重ね整理すると，だいたい以下であると推測しました．

- ID:00Aをもとに暗号化したメッセージがID:0AC？
- ID:00Aのフォーマットについて　00A: XX YY ZZ F5 64 01 00 00
    - XXは減少カウンタ/ランダム
    - YYは増加カウンタ/ランダム
    - ZZは数十秒程度で1増えるカウンタ
    - 4バイト目以降は固定
- ID:0ACのペイロードはすべて暗号化されている
- ID:0DCを連続で送信するとID:00AのXXとYYがカウンタの役割を果たす
    - エラーカウンタ？このときもラジコンカーは正常に走行する

これらから，単純に以下のようになり済ませる可能性を推測．

- 1. ID:00AのZZが00~FFのいずれの場合のメッセージをすべてキャプチャしてなりすます．それを記録した辞書を作成し，現在のバスをSniffし，上書きする．
    - 結論：かなり大変なため今回はこれを試さず

- 2. ID:0DCを連続でそうしんしまくり，このときのID:00Aをなりすます．ID:0ACはID:00Aをもとに暗号化していると考えられるので，1.よりもパターンが少なく済む？
    - 結論：適当なスクリプトを書いたがうまく行かず（あんまり検証する時間もなかった）


#### ZIPファイルの解読（パスワード解析）

一日目競技終了後NVさんがjohnを使ってZIPファイルを解読してくれました．

ご存知の方ば多いとは思いますが，johnというハッシュ推測ツールがあり，さらにパスワードリスト（辞書）にはrockyouというこれまた様々なパスワードを詰め込んだものがあります．

対象のZIPファイルのハッシュ値は以下でした．

```
$ > ./zip2john ../CarHack/ADC_s32k144-c3.zip
ADC_s32k144-c3.zip:$zip2$*0*3*0*42ba75f53e5c44709c693155e955c828*4f02*25d3*ZFILE*/home/teshiba/CarHack/ADC_s32k144-c3.zip*0*4d*b1a5363f4210c4f60ef1*$/zip2$:::::ADC_s32k144-c3.zip
```

こちらで解析を試みたところ，以下のようにパスワードが解読できます．

```
> $ ./zip2john ../CarHack/ADC_s32k144-c3.zip > hash.txt
> $ john --wordlist=rockyou.lst hash.txt
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 4x SSE2])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:02 0.21% (ETA: 03:20:55) 0g/s 8500p/s 8500c/s 8500C/s 
S3cur1ty         (ADC_s32k144-c3.zip)
1g 0:00:03:29 DONE (2018-11-02 03:08) 0.004784g/s 10053p/s 10053c/s 10053C/s TEAMOCRUZ..R679FUKU
Session completed
```

rockyouは100MBを超える辞書なのですが，私の仮想環境でも割とすぐに全探索できました．さて，以上の結果からパスワードは「S3cur1ty」であることがわかりました．これはSecurityのLeet変換であることが分かりました

https://ja.wikipedia.org/wiki/Leet

#### バイナリ

さて，本題のバイナリです．

引きずり出したバイナリの解析をいろいろ試してみました．

とりあえずobjdumpでヘッダを確認すると

```
> $ objdump -h ADC_s32k144-c3.elf

ADC_s32k144-c3.elf:	file format ELF32-arm-little

Sections:
Idx Name          Size      Address          Type
  0               00000000 0000000000000000 
  1 .interrupts   00000400 0000000000000000 DATA 
  2 .flash_config 00000010 0000000000000400 DATA 
  3 .text         00001744 0000000000000410 TEXT DATA 
  4 .interrupts_ram 00000400 000000001fff8000 BSS
  5 .data         00000000 000000001fff8400 DATA 
  6 .code         00000000 000000001fff8400 DATA 
  7 .bss          000002a0 0000000020000000 BSS
  8 .heap         00000400 00000000200002a0 BSS
  9 .stack        00000400 00000000200006a0 BSS
 10 .ARM.attributes 00000031 0000000000000000 
 11 .comment      00000070 0000000000000000 
 12 .debug_info   0000044c 0000000000000000 
 13 .debug_abbrev 000001fd 0000000000000000 
 14 .debug_loc    000002f1 0000000000000000 
 15 .debug_aranges 00000080 0000000000000000 
 16 .debug_line   00000257 0000000000000000 
 17 .debug_str    0000024e 0000000000000000 
 18 .debug_frame  000000a0 0000000000000000 
 19 .shstrtab     000000da 0000000000000000 
 20 .symtab       00001590 0000000000000000 
 21 .strtab       00001180 0000000000000000 
```

このあとradare2やobjdumpをいろいろ試してみました．

以下にCAN受信部分の逆アセンブリの結果(i386)を示します．

```
0000176d <can_receive_msg>:
    176d:	b4 17                	mov    $0x17,%ah
    176f:	4d                   	dec    %ebp
    1770:	d5 f8                	aad    $0xf8
    1772:	c0                   	(bad)  
    1773:	30 d5                	xor    %dl,%ch				; //compare?
    1775:	f8                   	clc    
    1776:	c4 30                	les    (%eax),%esi
    1778:	d5 f8                	aad    $0xf8
    177a:	c0 60 c3 f3          	shlb   $0xf3,-0x3d(%eax)
    177e:	8a 43 c6             	mov    -0x3a(%ebx),%al
    1781:	f3 03 46 03          	repz add 0x3(%esi),%eax
    1785:	60                   	pusha  
    1786:	46                   	inc    %esi				; while(True){
    1787:	71 be                	jno    1747 <can_transmit_msg+0x9e>	;//transmit?
    1789:	b1 05                	mov    $0x5,%cl
    178b:	30 00                	xor    %al,(%eax)				;
    178d:	22 4f f0             	and    -0x10(%edi),%cl
    1790:	ff 31                	pushl  (%ecx)
    1792:	12 f0                	adc    %al,%dh
    1794:	03 03                	add    (%ebx),%eax
    1796:	01 f1                	add    %esi,%ecx
    1798:	01 04 08             	add    %eax,(%eax,%ecx,1)
    179b:	bf 21 46 01 f1       	mov    $0xf1014621,%edi
    17a0:	32 04 c3             	xor    (%ebx,%eax,8),%al
    17a3:	f1                   	icebp  
    17a4:	03 03                	add    (%ebx),%eax
    17a6:	55                   	push   %ebp
    17a7:	f8                   	clc    
    17a8:	24 40                	and    $0x40,%al
    17aa:	db 00                	fildl  (%eax)
    17ac:	01 32                	add    %esi,(%edx)
    17ae:	24 fa                	and    $0xfa,%al
    17b0:	03 f3                	add    %ebx,%esi
    17b2:	b2 42                	mov    $0x42,%dl
    17b4:	00 f8                	add    %bh,%al
    17b6:	01 3f                	add    %edi,(%edi)
    17b8:	eb d1                	jmp    178b <can_receive_msg+0x1e>		;
    17ba:	04 4b                	add    $0x4b,%al
    17bc:	10 22                	adc    %ah,(%edx)
    17be:	d3 f8                	sar    %cl,%eax
    17c0:	80 10 99             	adcb   $0x99,(%eax)
    17c3:	68 1a 63 00 20       	push   $0x2000631a
    17c8:	70 bc                	jo     1786 <can_receive_msg+0x19>	; }//while loop
    17ca:	70 47                	jo     1813 <ADC_init+0x42>
```

送信部分

```
000016a9 <can_transmit_msg>:
    16a9:	b5 2b                	mov    $0x2b,%ch
    16ab:	4f                   	dec    %edi
    16ac:	3b 6b 23             	cmp    0x23(%ebx),%ebp
    16af:	f0 01 03             	lock add %eax,(%ebx)
    16b2:	3b 63 43             	cmp    0x43(%ebx),%esp
    16b5:	79 03                	jns    16ba <can_transmit_msg+0x11>
    16b7:	b3 46                	mov    $0x46,%bl
    16b9:	1d 4f f0 ff 35       	sbb    $0x35fff04f,%eax
    16be:	00 21                	add    %ah,(%ecx)
    16c0:	11 f0                	adc    %esi,%eax
    16c2:	03 03                	add    (%ebx),%eax
    16c4:	05 f1 23 02 04       	add    $0x40223f1,%eax
    16c9:	bf 01 35 47 f8       	mov    $0xf8473501,%edi
    16ce:	22 30                	and    (%eax),%dh
    16d0:	c3                   	ret    
    16d1:	f1                   	icebp  
    16d2:	03 04 ab             	add    (%ebx,%ebp,4),%eax
    16d5:	00 03                	add    %al,(%ebx)
    16d7:	f1                   	icebp  
    16d8:	40                   	inc    %eax
    16d9:	23 03                	and    (%ebx),%eax
    16db:	f5                   	cmc    
    16dc:	00 33                	add    %dh,(%ebx)
    16de:	16                   	push   %ss
    16df:	f8                   	clc    
    16e0:	01 2f                	add    %ebp,(%edi)
    16e2:	d3 f8                	sar    %cl,%eax
    16e4:	88 e0                	mov    %ah,%al
    16e6:	e4 00                	in     $0x0,%al
    16e8:	a2 40 4e ea 02       	mov    %al,0x2ea4e40
    16ed:	02 c3                	add    %bl,%al
    16ef:	f8                   	clc    
    16f0:	88 20                	mov    %ah,(%eax)
    16f2:	43                   	inc    %ebx
    16f3:	79 01                	jns    16f6 <can_transmit_msg+0x4d>
    16f5:	31 8b 42 e2 dc 17    	xor    %ecx,0x17dce242(%ebx)
    16fb:	4a                   	dec    %edx
    16fc:	1b 04 43             	sbb    (%ebx,%eax,2),%eax
    16ff:	f0 44                	lock inc %esp
    1701:	63 c2                	arpl   %ax,%dx
    1703:	f8                   	clc    
    1704:	80 30 03             	xorb   $0x3,(%eax)
    1707:	68 9b 04 23 f0       	push   $0xf023049b
    170c:	60                   	pusha  
    170d:	43                   	inc    %ebx
    170e:	c2 f8 84             	ret    $0x84f8
    1711:	30 d2                	xor    %dl,%dl
    1713:	f8                   	clc    
    1714:	80 30 c3             	xorb   $0xc3,(%eax)
    1717:	f3 03 63 08          	repz add 0x8(%ebx),%esp
    171b:	2b 0e                	sub    (%esi),%ecx
    171d:	d0 10                	rclb   (%eax)
    171f:	46                   	inc    %esi
    1720:	05 21 d0 f8 80       	add    $0x80f8d021,%eax
    1725:	20 4f f4             	and    %cl,-0xc(%edi)
    1728:	7a 73                	jp     179d <can_receive_msg+0x30>
    172a:	c2 f3 03             	ret    $0x3f3
    172d:	62 00                	bound  %eax,(%eax)
    172f:	bf 01 3b fc d1       	mov    $0xd1fc3b01,%edi
    1734:	01 39                	add    %edi,(%ecx)
    1736:	03 d0                	add    %eax,%edx
    1738:	08 2a                	or     %ch,(%edx)
    173a:	f2 d1 00             	repnz roll (%eax)
    173d:	20 f0                	and    %dh,%al
    173f:	bd 05 4a d2 f8       	mov    $0xf8d24a05,%ebp
    1744:	80 30 23             	xorb   $0x23,(%eax)
    1747:	f0 70 63             	lock jo 17ad <can_receive_msg+0x40>
    174a:	43                   	inc    %ebx
    174b:	f0 10 63 c2          	lock adc %ah,-0x3e(%ebx)
    174f:	f8                   	clc    
    1750:	80 30 4f             	xorb   $0x4f,(%eax)
    1753:	f0 ff 30             	lock pushl (%eax)
    1756:	f0                   	lock
    1757:	bd                   	.byte 0xbd
```

また，おそらく解読すべきであろう関数は"challenge_three_unique"という関数でした．

※長いのでGistに投げました．

<script src="https://gist.github.com/himitu23/cb04d05f8fb92f44dbba580a8c943975.js"></script>

また，IDAを利用して逆コンパイラもやりました（そこまでして解けないのか？と怒られそう）

このバイナリにはこれらの関数が含まれていました．

```c
// Function declarations

void __noreturn Reset_Handler();
int __fastcall Speck_Encrypt_32(int result, int a2, unsigned __int16 *a3, unsigned __int16 *a4);
int __fastcall Speck_Encrypt_48(int a1, int *a2, _DWORD *a3, int a4);
int __fastcall Speck_Encrypt_64(int result, int a2, int *a3, int *a4);
int __fastcall Speck_Encrypt_96(int a1, int a2, int a3, int a4);
int __fastcall Speck_Encrypt_128(__int64 a1, __int64 *a2, __int64 *a3);
int __fastcall Speck_Decrypt_32(char a1, int a2, unsigned __int16 *a3, _WORD *a4);
int __fastcall Speck_Decrypt_48(char a1, int a2, _DWORD *a3, int a4);
int __fastcall Speck_Decrypt_64(char a1, int a2, int *a3, int *a4);
int __fastcall Speck_Decrypt_96(char a1, int a2, int a3, int a4);
int __fastcall Speck_Decrypt_128(char a1, int a2, __int64 *a3, __int64 *a4);
signed int __fastcall Speck_Init(int a1, unsigned int a2, int a3, char *a4);
int __fastcall Speck_Encrypt(int a1, int a2, int a3);
int __fastcall prepare_joystick_msg(int a1, _BYTE *a2);
int __cdecl main(int argc, const char **argv, const char **envp);
int SOSC_init_8MHz();
int SPLL_init_160MHz();
void NormalRUNmode_80MHz();
void rtc_init();
__int64 get_time_tick();
__int64 get_time_seconds();
signed int __fastcall challenge_three_unique(_DWORD *a1, int a2);
signed int current_challenge_number();
int can_init();
signed int __fastcall can_transmit_msg(int a1);
int can_has_msg();
int __fastcall can_receive_msg(int a1);
int ADC_init();
int __fastcall convertAdcChan(char a1);
int adc_complete();
int read_adc_chx();
signed int SystemInit();
int *init_data_bss();
int_t __fastcall memcmp(const void *src1, const void *src2, size_t n);
void *__fastcall memcpy(void *dst, const void *src, size_t n);
void *__fastcall memset(void *dst, int_t val, size_t n);
void __fastcall _fill_mem(void *dst, int_t val, uint32_t n);
```

ここでchallenge_three_unique関数はこんな感じ．

※これもGistに投げました

<script src="https://gist.github.com/himitu23/3b5a9b558a4aff844b9cc57e841452ec.js"></script>

ここで最も怪しい部分は

```C
if ( v11 != 8 || Speck_Encrypt((int)&spk_8_byte_level_three, (int)&v36, (int)&v41) )
```

のSpeck_Encrypt関数を読んでいる部分です．

```C
int __fastcall Speck_Encrypt(int a1, int a2, int a3)
{
  (*(void (__fastcall **)(_DWORD, int, int, int))(a1 + 4))(*(unsigned __int8 *)(a1 + 15), a1 + 48, a2, a3);
  return 0;
}
```

ちょっとこの関数がなにをしているのかわからないのですが，変数spk_8_byte_level_threeが暗号鍵であり，Speck_Encrypt関数は必要な暗号化関数を呼び出しているのでしょうか．

変数定義は

```C
_UNKNOWN spk_8_byte_level_three; // weak
```

となっています．_UNKNOWNなのでわからないということでしょうか．これは以下の初期化をしそうな関数で先に初期化されてそうな感じです．

```C
Speck_Init((int)&spk_8_byte_level_three, 4u, 0, (char *)&v41);
```

さらに，この関数の引数は

```C
signed int __fastcall Speck_Init(int a1, unsigned int a2, int a3, char *a4)
```

こんな感じなので，おそらくspk_8_byte_level_threeのメモリアドレスに直接何かしらの値をいれて初期化をしているのかと思いました．なので怪しいのはここら辺なのではないかと想うのですが，これ以上はわかりませんでした．



```C
　...
  dst = 0LL;
　...
　if ( v15 )
  {
    v16 = a4;
    v17 = (__int64 *)&dst;
    do
    {
      v18 = v17;
      ++v17;
      memcpy(v18, v16, v8 >> 3);
      v16 += v14;
    }
    while ( v17 != (__int64 *)(&dst + v15) );
    memcpy((void *)(v9 + 48), &dst, v8 >> 3);//先にv9 = a1;としている
  }
  else
  {
    memcpy((void *)(a1 + 48), &dst, v8 >> 3);
  }
 ...
```



#### Speckによる暗号化について

競技中には実際に暗号化を解いてなりすましを行うプログラムの作成までいきませんでした．

しかし，Speckという暗号化が行われていることはわかっていましたので，このSpeckについても競技時間内に簡単に調べていました．

Speckとは？なんかSimonってのもあるらしい．

https://github.com/nsacyber/simon-speck

いろいろ調査していくと上記Githubが．

さらにSpeck自体の論文もみつかりました．

https://eprint.iacr.org/2013/404.pdf

こちらの論文に実装例についても書かれています．(pt[]が暗号対象のメッセージ，ct[]が暗号化されたメッセージ，kが鍵となっています)

```c
#define LCS _lrotl  //left circular shift
#define RCS _lrotr  //right circular shift
#define u64 unsigned long long
#define R(x,y,k) (x=RCS(x,8), x+=y, x^=k, y=LCS(y,3), y^=x)

void Speck128Encrypt(u64 pt[], u64 ct[], u64 k[])
 {
　　u64 i;
   ct[0]=pt[0]; ct[1]=pt[1];
   for(i=0; i<32; i++) R(ct[1], ct[0], k[i]);
 }
```

 もし競技中に解析までうまくい行けば，

- 暗号鍵を発見する
- Speckの暗号化を試す（解析でおそらく128bit->128bit暗号だろうと推測）
- 現在流れているメッセージ(CANID:00A?)を鍵としてCANID:0ACを作成？するスクリプトを作成

という感じでしょうか．

### Challenge4 (300 pt.)

```
** challenge4
File1: candump-level4.log
File2: ADC_s32k144 -c4.zip
Point: 300
```

チャレンジ3同様ZIPファイルがありますね．

もちろんチャレンジ3が解けていないので．4も解けていないのですが，後日わかったことを残しておきます．

まず，ZIPのパスワードについてはわかりませんでしたので，こそっと教えてもらいました．

中にはやはりバイナリファイルが一つ．

ちなみにハッシュ値はこれでした．

```
> $ ./zip2john ../CarHack/ADC_s32k144-c4.zip
ADC_s32k144-c4.zip:$zip2$*0*3*0*22165c2abb12e7fd2a99f43ed1a91057*fa09*24d9*ZFILE*/home/teshiba/CarHack/ADC_s32k144-c4.zip*0*4e*2a44cf1768a739b3501f*$/zip2$:::::ADC_s32k144-c4.zip
```
パスワードはあるキーワードをLeet変換したものでした．Challenge3のLeet変換がヒントでしたが，実際にはこれはノーヒントだったのでなかなかハードです．

#### バイナリ

こちらも少しバイナリを覗いてみました．

objdump -hの結果

```
> $ objdump -h ADC_s32k144-c4.elf 

ADC_s32k144-c4.elf:	file format ELF32-arm-little

Sections:
Idx Name          Size      Address          Type
  0               00000000 0000000000000000 
  1 .interrupts   00000400 0000000000000000 DATA 
  2 .flash_config 00000010 0000000000000400 DATA 
  3 .text         0000163c 0000000000000410 TEXT DATA 
  4 .interrupts_ram 00000400 000000001fff8000 BSS
  5 .data         00000000 000000001fff8400 DATA 
  6 .code         00000000 000000001fff8400 DATA 
  7 .bss          00000514 0000000020000000 BSS
  8 .heap         00000404 0000000020000514 BSS
  9 .stack        00000400 0000000020000918 BSS
 10 .ARM.attributes 00000031 0000000000000000 
 11 .comment      00000070 0000000000000000 
 12 .debug_info   0000044c 0000000000000000 
 13 .debug_abbrev 000001fd 0000000000000000 
 14 .debug_loc    000002f1 0000000000000000 
 15 .debug_aranges 00000080 0000000000000000 
 16 .debug_line   00000257 0000000000000000 
 17 .debug_str    0000024e 0000000000000000 
 18 .debug_frame  000000a0 0000000000000000 
 19 .shstrtab     000000da 0000000000000000 
 20 .symtab       000015a0 0000000000000000 
 21 .strtab       00001186 0000000000000000 
```



また，逆コンパイラして確認した関数はこちら．

```c
// Function declarations

void __noreturn Reset_Handler();
int __fastcall Speck_Encrypt_32(int result, int a2, unsigned __int16 *a3, unsigned __int16 *a4);
int __fastcall Speck_Encrypt_48(int a1, int *a2, _DWORD *a3, int a4);
int __fastcall Speck_Encrypt_64(int result, int a2, int *a3, int *a4);
int __fastcall Speck_Encrypt_96(int a1, int a2, int a3, int a4);
int __fastcall Speck_Encrypt_128(__int64 a1, __int64 *a2, __int64 *a3);
int __fastcall Speck_Decrypt_32(char a1, int a2, unsigned __int16 *a3, _WORD *a4);
int __fastcall Speck_Decrypt_48(char a1, int a2, _DWORD *a3, int a4);
int __fastcall Speck_Decrypt_64(char a1, int a2, int *a3, int *a4);
int __fastcall Speck_Decrypt_96(char a1, int a2, int a3, int a4);
int __fastcall Speck_Decrypt_128(char a1, int a2, __int64 *a3, __int64 *a4);
signed int __fastcall Speck_Init(int a1, unsigned int a2, int a3, char *a4);
int __fastcall Speck_Encrypt(int a1, int a2, int a3);
int __fastcall prepare_joystick_msg(int a1, _BYTE *a2);
int __cdecl main(int argc, const char **argv, const char **envp);
int SOSC_init_8MHz();
int SPLL_init_160MHz();
void NormalRUNmode_80MHz();
void rtc_init();
__int64 get_time_tick();
__int64 get_time_seconds();
signed int __fastcall challenge_four_unique(_DWORD *a1, int a2);
signed int current_challenge_number();
int can_init();
signed int __fastcall can_transmit_msg(int a1);
int can_has_msg();
int __fastcall can_receive_msg(int a1);
int ADC_init();
int __fastcall convertAdcChan(char a1);
int adc_complete();
int read_adc_chx();
signed int SystemInit();
int *init_data_bss();
int_t __fastcall memcmp(const void *src1, const void *src2, size_t n);
void *__fastcall memcpy(void *dst, const void *src, size_t n);
void *__fastcall memset(void *dst, int_t val, size_t n);
void __fastcall _fill_mem(void *dst, int_t val, uint32_t n);
```


これはChallenge3とほぼ同じ関数です．唯一異なるのがunique関数みたいです．challenge_four_unique関数はこちら（Gist）

<script src="https://gist.github.com/himitu23/c3793b2a7230c4fb2ad99edd96b7454d.js"></script>

### Challenge5 (400 pt.)

```
** challenge5
File: candump-level5.log
Point: 400
```

最後はログファイル一つだけなようです．

中身はこんな感じでした．

```
...
(1533129089.360862) slcan0 0AA#341FB94624EA
(1533129089.590563) slcan0 0AA#ABABABABABAB
(1533129089.590588) slcan0 001#3FAA00A1BDAE
(1533129089.590593) slcan0 001#1CAA326503AA
(1533129089.665513) slcan0 0AA#E02CA5C4916E
(1533129089.970509) slcan0 0AA#3E05BB6265FE
(1533129090.053125) slcan0 0AA#324FD26F81EA
(1533129090.357986) slcan0 0AA#A892177DA890
(1533129090.662907) slcan0 0AA#FD493958D08A
(1533129090.967815) slcan0 0AA#DA26998053CD
(1533129091.050468) slcan0 0AA#EBD4DD184981
(1533129091.238912) slcan0 0AA#ABABABABABAB
(1533129091.238945) slcan0 001#3FAA00A1BDAE
(1533129091.238948) slcan0 001#1CAA6A30AA6D
(1533129091.355445) slcan0 0AA#0DE00D067D20
(1533129091.660199) slcan0 0AA#5BED4529D410
...
```

よく見るとCANID:0AAに混ざってCANID:001がところどころ混ざっているようです．　

## 感想とか

今回は昨年と違い，かなり本格的な自動車ハックという感じでした．賞品として，今回ドローンをいただけましたが，今回のチャレンジ環境をまるごと持って帰ってもいいとおっしゃっており，個人的に欲しかったです．それだけでも出る価値はありました．

実際に解析の経験が非常に求められていたのではないでしょうか．私も来年から会社にてそういった業務にも取り組む可能性があるので，今回はできるだけしっかり調べてみました．次回に活かせればいいですが，，

もし，この記事おかしい！とか，具体的にコメントしたい！とか，もはや答え教えてくれる！とかありましたら，以下でプルリクもしくはコメントなどでくれても大丈夫です．

https://github.com/himitu23/blog_article

また，今回私はCODE BLUEではこのチャレンジだけではなく，翻訳スタッフをさせていただいたり，講演を聞いたり，その他のCTFに参加したりできました．DeloitteのCTFではTsubasaくんが2位だったらしい！

さらに今まで関わってきた多くの方にも会うことができ，非常に実りある経験となりました．来年も参加できるように勉強を続けていきたいです！最後の学生生活残りもやるぞ～

![](https://raw.githubusercontent.com/himitu23/blog_article/master/20181105_CarHackCTF_xINTCTF_Writeup/img/IMG_4844.JPG)

Thank you CODE BLUE!