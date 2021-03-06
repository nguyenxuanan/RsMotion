RsMotion
======================
RsMotionはコマンド式サーボモータRTCをGUIから制御するRTコンポーネントです    
サーボモータのON/OFF、サーボの移動指令などを出すことができます    
使用するサーボモータの個数や、GUIの構成などを制御対象に合わせて柔軟に設定できます  
主に双葉電子工業（株）のコマンド方式サーボである[RSシリーズ][rs]の制御を対象としています  

本RTCを用いたデモンストレーションをYoutubeでご覧になれます  
[サーボモータ制御用GUI：RsMotoin][video]  
[video]: http://www.youtube.com/watch?v=ZUDtK24dK-I 

[rs]: http://www.futaba.co.jp/robot/command_type_servos/index.html

<img src="http://cloud.github.com/downloads/HiroakiMatsuda/RsMotion/readme_02.png" width="400px" />    

動作確認環境
------
Python:  
2.6.6  

OS:  
Windows 7 64bit / 32bit  
Ubuntu 10.04 LTS / 12.04 LTS 32bit  

対応RTC:
RsServoManager

ファイル構成
------
RsMotion 
│―RsMotion.py  
│―tkrsmotion.py  
│―ini   
│　　│―gui.ini    
│  
│―rtc.conf  

* RsMotion.py  
RTC本体です  
* tkrsmotion.py  
RTCのGUIをTkinterにて生成しています
* gui.ini  
サーボモータの個数や指令値のリミットなどを設定します  
* rtc.conf  
ポートの設定や動作周期を設定できます  

注:本RTCにおいてユーザーが操作すると想定しているファイルのみ説明しています  

ファイルの設定はiniファイルを通して行えるので、簡単に設定を変えられます  
iniファイルの設定はInitialize時に読み込むので、設定を変更した場合はRTCを再起動してください

RTCの構成
------  
<img src="http://cloud.github.com/downloads/HiroakiMatsuda/RsMotion/readme_01.png" width="400px" />    
RsServoManagerなどの対応するRTCを接続することでGUIを通していサーボモータを制御できます  

* motion port :OutPort  
データ型; TimedLongSeq  
[Flag, Id, Position, Time]  
 ・  `Flag` :  モーションの同期用フラグ
0:同期なし
移動指令は全て同期なしで指令  
 ・  `Id` :  サーボID  
指令を送るサーボモータのIDを指定します      
 ・  `Position` :  移動位置  
サーボモータへ角度 [0.1 deg]を指定します     
 ・  `Time` :  移動時間  
指定位置までの移動時間 0~16383 [msec]の間で指定します  

* on_off port :OutPort  
データ型; TimedLongSeq  
[Flag, Id, Torque mode]  
 ・  `Flag` :  モーションの同期用フラグ  
0:同期なし  
1:同期あり  
Flagを1にすると設定したサーボモータ全てに同時に指令を送ります  
個別のトルク保持指令ボタンを押した場合は同期なしモード  
全体のトルク保持指令ボタンを押した場合は同期モード   
 ・  `Id` :  サーボID  
指令を送るサーボモータのIDを指定します    
 ・  `Toruque mode` :  トルクモード指定  
0:トルクをオフにします（ボタンが赤色のとき）  
1:トルクをオンにします（ボタンが緑色のとき）  
 
* sensor port :InPort  
データ型; TimedLongSeq  
[Id, Angle, Time, Speed, Load, Temperature, Voltage]  
 ・  `'Angle` :  現在位置 [0.1 deg]  
 ・  `'Time` :  現在時間 [0.1 sec]  
現在時間はサーボが指令を受信し、移動を開始してからの経過時間です   
 ・  `'Speed` :  現在スピード [deg / sec]  
現在の回転スピードを取得できますが、この値は目安です。  
 ・  `'Load` :  現在負荷 [mA]  
サーボに供給されている電流を返しますが、この値は目安です。  
 ・  `Tempreture` :  現在温度 [degree celsius]  
この値はセンサの個体差により±3 [degree celsius] 程度の誤差があります  
 ・  `'Voltage` :  現在電圧 [10 mV]  
この値はセンサの個体差により±0.3 [V]程度の誤差があります   

使い方
------
###1. 使用するサーボモータの設定する###
gui.iniをテキストエディタなどで開き編集します  


  ・ ```servo_num = X```  
使用するサーボモータの個数を設定します  

  ・ ```move_time =  X```  
サーボモータの移動時間 を0~16383 [msec]の間で設定します   
  
ここからの作業は使用するサーボモータの個数だけ行なっていください  
4つのサーボモータを使用する場合は、id1からid4まで設定します  
この際にid_は1から連続した値を指定してください   

・ ```id1_label = XXX```  
GUIに表示するサーボモータの名前を付けます  
・ ```id1_id = X ```    
id1番に登録するサーボモータのIDを設定します  
・ ```id1_min = X ```    
id1番のサーボモータの移動最小値を設定します  
・ ```id1_max = X ```  
id1番のサーボモータの移動最大値を設定します  
・ ```id1_column = X ```  
id1番のサーボモータのGUI上の表示位置（列）を設定します  
・ ```id1_row = X ```    
id1番のサーボモータのGUI上の表示位置（行）を設定します 

###2. サーボモータ制御用RTCと接続する###
1. RsMotion.py起動する  
ダブルクリックするなどして起動してください  
2. サーボモータと接続する    
RsServoManagerなどのサーボモータ制御用RTCと接続します  

  
###3. サーボモータを動かす###
1. トルク保持命令を発行する  
下図のGUIのボタンを押すとサーボのトルク保持命令が発行されます  
緑のときにトルクON、赤のときにトルクOFFとなります  
一番上のボタンは全サーボモータにトルク保持命令を発行します      
<img src="https://github.com/downloads/HiroakiMatsuda/RsMotion/readme_03.png" width="400px" />  
<img src="https://github.com/downloads/HiroakiMatsuda/RsMotion/readme_04.png" width="400px" />    

2. サーボモータの移動指令を発行する  
各スライダーバーを動かすことでサーボモータに移動指令を発行できます  
<img src="https://github.com/downloads/HiroakiMatsuda/RsMotion/readme_05.png" width="400px" />    

3. 現在のセンサ値を確認する
GUIの右側のテキストボックスにはサーボのセンサ値が表示されています   
ID, Position, Current, Voltage, Load, Temperatureの順に表示されます  
各値は以下のようになっています    
Position', 現在位置 [0.1 deg]を返します  
'Load', 現在負荷 [mA]を返します。サーボに供給されている電流を返しますが、この値は目安です  
'Tempreature', 現在温度 [degree celsius]を返します。この値はセンサの個体差により±3 [degree celsius] 程度の誤差があります  
'Voltage', 現在電圧 [10 mV]を返します。この値はセンサの個体差により±0.3 [V]程度の誤差があります  
      
以上が本RTCの使い方となります  

ライセンス
----------
Copyright &copy; 2012 Hiroaki Matsuda  
Licensed under the [Apache License, Version 2.0][Apache]  
Distributed under the [MIT License][mit].  
Dual licensed under the [MIT license][MIT] and [GPL license][GPL].  
 
[Apache]: http://www.apache.org/licenses/LICENSE-2.0
[MIT]: http://www.opensource.org/licenses/mit-license.php
[GPL]: http://www.gnu.org/licenses/gpl.html