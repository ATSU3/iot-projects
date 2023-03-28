<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# 楽器を作ってみる (WIP)

本プロジェクトは[木箱-kibaco-さん](https://kitorina.com/wp/kibaco/)にご協力いただき進めているプロジェクトになります。
栗山町を拠点に活動する[木箱-kibaco-さん]と電子機器を組み合わせた新たな楽器を制作し、町内のライブ活動に活用したい、という個人の課題を、最終的に人口が減少している中で、アーティストを中心に「栗山町 = クリエイティブなまち」を広めることで関係人口創出し、人口増加をしていくという栗山町の課題の解決をに繋げていきたいと思います。


## ステッピングモータを楽器として利用する
ステッピングモーターは時計の秒針のように、一定の角度ずつ回転するモーターです。
ステッピングモーターが回転する際に発生する振動を利用して音を鳴らしております。

## MIDIについて

今回プロジェクトを進める上でMIDIについての知識が少なかったため以下のサイトで学習させていただき、とても参考になりました。

- [MIDIのデータ構造](http://www1.plala.or.jp/yuuto/midi/p0200.html#:~:text=MIDI%20%E3%81%A7%E3%81%AF%E3%80%81%E6%BC%94%E5%A5%8F%E6%83%85%E5%A0%B1%E3%82%92,%E3%81%A6%E9%80%81%E3%82%89%E3%82%8C%E3%82%8B%E3%82%8F%E3%81%91%E3%81%A7%E3%81%99%E3%80%82)


<!-- ステッピングモーターから発生する音で楽器を作成する。 -->

<!-- kibakoの西村さんとのアイディアスケッチを参考に作成していく。 -->

## 電子回路

本プロジェクトではArduinoとCNCシールドを用いてステッピングモーターの制御を行っております。

![](../images/prototype/prototype2/circuit/circuit_1.jpg#center)

使用したCNCシールドとArduinoのピンの対応は以下になります。

![](../images/prototype/prototype2/circuit/circuit_2.jpg#center)

|  Arduino Uno  |     CNCシールド     |  
| ------------- | ------------------ |
|      2        |  Step Pulse X-Axis |
|      3        |  Step Pulse Y-Axis |
|      4        |  Step Pulse Z-Axis |
|      5        |  Direction X-Axis  |
|      6        |  Direction Y-Axis  |
|      7        |  Direction Z-Axis  |
|      8        |  Stepper Enable/Disable  |

### ステッピングモータ・モータードライバの設定

#### モータードライバ(DRV8825)の電流調整

↓以下の資料が大変参考になります。
<br>
参考資料: [Drv8825 Stepper Motor Driver Arduino Tutorial](https://lastminuteengineers.com/drv8825-stepper-motor-driver-arduino-tutorial/)

ステッピングモーターに流れる最大電流は、定格電流を超えないように制限する必要がありますので、ドライバーのポテンショメーターで以下のように調整します。

![](../images/prototype/prototype2/circuit/stepper_2.jpg#center)


この方法では、「ref」ピンの電圧（Vref）を測定することで電流制限を設定します。

![](../images/prototype/prototype2/circuit/stepper_3.jpg#center)

1. NEMA 17 200steps/rev、12V 350mAを使用しているため、定格電流は

2. 3本のマイクロステップ選択ピンを外して、ドライバをフルステップモードにします。

3. STEP入力をクロックしないようにして、モーターを定位置に保持する。

4. 金属製のトリマーポット自体の電圧（Vref）を測定しながら調整する。

5. 5.次の式でVref電圧を調整します。

```
Current Limit = Vref x 2
```

Nema 17ステッピングモーターの定格は350mAなので、基準電圧を175mVに調整することになります。

![](../images/prototype/prototype2/circuit/stepper_4.jpg#center)

### FLIPでファームフェアの書き換え

電子楽器を接続する際MIDI接続に比べUSBで接続できる方が汎用性が高いため、Arduinoのファームウェアを書き換え、USB接続のMIDIOUTができる端末にしていきたいと思います。

ArduinoをUSB MIDIデバイスとして設定したい場合、AVRチップのファームウェアを変更する必要があります。その際にATMEL製のファームウェア書き込み用ソフトウェア FLIP 3.4.7を使用します。

[FLIPダウンロードリンク](https://www.microchip.com/en-us/development-tool/flip)

![](../images/prototype/prototype1/flip/flip_1.jpg#center)

アプリのホーム画面は以下になります。

![](../images/prototype/prototype1/flip/flip_2.jpg#center)

書き込み対象のデバイス名を選択します。

![](../images/prototype/prototype1/flip/flip_3.jpg#center)

今回はArduino Unoを使用し作成しており、「ATmega16U2」が埋め込まれているためこちらを選択します。

![](../images/prototype/prototype1/flip/flip_13.jpg#center)

以下のようにマイコンの表面に「ATmega16U2」のように印刷されております。

![](../images/prototype/prototype1/flip/flip_12.jpg#center)

書き込み対象のマイコン名を選択後、USBマークを選択して「USB」を選択します。

![](../images/prototype/prototype1/flip/flip_4.jpg#center)

「USB Port Connection」ウィンドウが開くため「Open」を選択します。

![](../images/prototype/prototype1/flip/flip_5.jpg#center)

選択されるとHEX File欄の (ATMEL)の文字が青くなります。

![](../images/prototype/prototype1/flip/flip_6.jpg#center)

「File」=>「Load HEX File」から読み込み対象のファイルを選択します。

![](../images/prototype/prototype1/flip/flip_7.jpg#center)

「midi stepper.hex」を選択します。

![](../images/prototype/prototype1/flip/flip_8.jpg#center)

「Run」ボタンをクリックします。

![](../images/prototype/prototype1/flip/flip_9.jpg#center)

書き込みが完了すると、デバイスマネージャーで「MIDI Stepper」が確認できます。

![](../images/prototype/prototype1/flip/flip_10.jpg#center)

![](../images/prototype/prototype1/flip/flip_11.jpg#center)

![](../images/prototype/prototype1/flip/flip_14.jpg#center)

デバイスをArduino Unoとして認識して書き込みできるようにするためには、以下の写真のように2本のピンを繋ぎます。

![](../images/prototype/prototype1/flip/flip_15.jpg#center)


## プログラム

プログラムは[こちら](https://github.com/jzkmath/Arduino-MIDI-Stepper-Motor-Instrument/blob/master/Arduino%20Code/MIDI%20Stepper%20V1/MIDI_Stepper_V1.ino)のプロジェクトを参考にさせていただきました。

このプログラムでは、MIDIピッチ値を周波数に変換し、その周波数に対応するステッピングモーターの速度値を決定します。この速度値は、モーターが指定された周波数の音を生成するために必要なパルスの間隔を表します。

[pitchs.h](https://github.com/jzkmath/Arduino-MIDI-Stepper-Motor-Instrument/blob/master/Arduino%20Code/MIDI%20Stepper%20V1/pitches.h)を読み込ませており、プログラムで周波数に対応するステッピングモーターの速度値を決定します。


MIDIメッセージに応じて、各ステッピングモーターの速度を変更することができます。これは、MIDIノートオンメッセージが受信された場合に、ピッチに基づいてスピードを設定します。また、MIDIノートオフメッセージが受信された場合に、モータースピードをゼロに設定します。

```c++
#include <MIDI.h>
#include "pitches.h"

#define stepPin_M1 2
#define stepPin_M2 3
#define stepPin_M3 4

#define dirPin_M1 5 
#define dirPin_M2 6
#define dirPin_M3 7

#define enPin 8 

#define TIMEOUT 10000 
unsigned long motorSpeeds[] = {0, 0, 0, 0}; 
unsigned long prevStepMicros[] = {0, 0, 0, 0}; 
const bool motorDirection = LOW; 
bool disableSteppers = HIGH; 
unsigned long WDT; 
MIDI_CREATE_DEFAULT_INSTANCE(); 

void setup() 
{
  pinMode(stepPin_M1, OUTPUT);
  pinMode(stepPin_M2, OUTPUT);
  pinMode(stepPin_M3, OUTPUT);

  pinMode(dirPin_M1, OUTPUT);
  pinMode(dirPin_M2, OUTPUT);
  pinMode(dirPin_M3, OUTPUT);

  digitalWrite(dirPin_M1, motorDirection);
  digitalWrite(dirPin_M2, motorDirection);
  digitalWrite(dirPin_M3, motorDirection);

  pinMode(enPin, OUTPUT);

  MIDI.begin(MIDI_CHANNEL_OMNI); 
  MIDI.setHandleNoteOn(handleNoteOn); 
  MIDI.setHandleNoteOff(handleNoteOff); 
}

void loop() 
{
  MIDI.read(); 
  digitalWrite(enPin, disableSteppers); 
  singleStep(1, stepPin_M1); 
  singleStep(2, stepPin_M2);
  singleStep(3, stepPin_M3);

  if (millis() - WDT >= TIMEOUT)
  {
    disableSteppers = HIGH; 
  }
}

void handleNoteOn(byte channel, byte pitch, byte velocity) 
{
  disableSteppers = LOW; 
  motorSpeeds[channel] = pitchVals[pitch]; 
}

void handleNoteOff(byte channel, byte pitch, byte velocity) 
{
  motorSpeeds[channel] = 0; 
}

void singleStep(byte motorNum, byte stepPin)
{
  if ((micros() - prevStepMicros[motorNum] >= motorSpeeds[motorNum]) && (motorSpeeds[motorNum] != 0)) 
  { 
    prevStepMicros[motorNum] += motorSpeeds[motorNum];
    WDT = millis(); 
    digitalWrite(stepPin, HIGH);
    digitalWrite(stepPin, LOW);
  }
}
```

MIDIライブラリとpitches.hを読み込んでます。

```c++
#include <MIDI.h>
#include "pitches.h"
```

ステッピングモータ1~3のstepピン、dirピンの番号をわかりやすいように定義します。

```c++
#define stepPin_M1 2
#define stepPin_M2 3
#define stepPin_M3 4

#define dirPin_M1 5 
#define dirPin_M2 6
#define dirPin_M3 7
```

（EN）ピンにHIGHの信号が入力されると、モーターが動作します。8番ピンをわかりやすいようにenPinという名前で定義しております。

```c++
#define enPin 8
```

プログラムで使用されるタイムアウト値を定義するために必要な行です。このタイムアウト値は、何らかの処理が完了するまでに許容される最大待機時間、(このプログラムの場合10秒)を決定します。

```c++
#define TIMEOUT 10000 
```

それぞれ3つのステッピングモータの速度と前のステップの時間を記録して格納するための配列を宣言しています。

motorSpeeds配列は、各モータの速度をマイクロ秒単位で格納するために使用されます。各要素は、各ステッピングモータの現在の速度を保持するために使用します。

prevStepMicros配列は、各モータの前回のステップ時刻を格納するために使用されます。これらの値は、次のステップの間隔を決定するために使用されます。前回のステップからの経過時間は、現在の時間と前回のステップ時刻の差分を取ることで計算されます。

それぞれ配列の一つ目の要素は使用しません。(index番号とモータの番号を合わせるため。)

```c++
unsigned long motorSpeeds[] = {0, 0, 0, 0}; 
unsigned long prevStepMicros[] = {0, 0, 0, 0}; 
```

以下の2つの変数は、ステッピングモーターの制御に関する情報を格納するために使用します。
motorDirectionは、ステッピングモーターの回転方向を指定するための変数で、LOWに設定されている場合は、モーターが正方向（通常は時計回り）に回転し、HIGHに設定されている場合は、逆方向（通常は反時計回り）に回転します。

disableSteppersは、ステッピングモーターの動作を有効または無効にするためのフラグで、HIGHに設定されている場合は、モーターは動作しなくなります。この変数は、handleNoteOn関数によってLOWに設定され、モーターを動作させるようになります。また、一定時間（TIMEOUTで定義された値）が経過すると、loop関数内の条件文によって再びHIGHに設定され、モーターを停止させます。

```c++
const bool motorDirection = LOW; 
bool disableSteppers = HIGH; 
```

WDT（Watchdog Timer）は、モーターを動かすステッピングモータードライバーが正しく動作していることを確認するために使用されます。この変数は、最後にステップが実行された時間を記録し、それが一定期間（TIMEOUT変数で定義されています）更新されなかった場合、モータードライバーが無効になります。これは、モータードライバーに問題がある場合、例えばステッピングモーターが詰まっている場合、モーターが破損するのを防ぐために役立ちます。

```c++
unsigned long WDT; 
```

MIDIライブラリを使用するため、MIDIライブラリで定義されたデフォルト設定を使用して、MIDIインスタンスを作成します。

```c++
MIDI_CREATE_DEFAULT_INSTANCE(); 
```


次に、void setup() 関数の中の処理

このプログラムの setup() 関数で初期化を行います。
まず、pinMode() 関数が使用して、各ステッピングモーターのステップピンと方向ピンを出力ピンに設定します。

```c++
  pinMode(stepPin_M1, OUTPUT);
  pinMode(stepPin_M2, OUTPUT);
  pinMode(stepPin_M3, OUTPUT);

  pinMode(dirPin_M1, OUTPUT);
  pinMode(dirPin_M2, OUTPUT);
  pinMode(dirPin_M3, OUTPUT);
```

次に、各モーターの方向を設定します。ここで、digitalWrite() 関数を使用して、各方向ピンに motorDirection 変数の値を書き込んでいます。motorDirection の初期値は LOW であるため、すべてのモーターは同じ方向に回転するように設定されます。
```c++
  digitalWrite(dirPin_M1, motorDirection);
  digitalWrite(dirPin_M2, motorDirection);
  digitalWrite(dirPin_M3, motorDirection);
```




タイムアウトが発生した場合にステッパーモータを無効にするために使用します。プログラムが長時間実行される場合、ステッパーモータを継続的に動かす必要がありますが、これはモータにとっては負荷が大きく、長時間使用すると過熱する場合があるため、一定の時間経過後、自動的にステッパーモーターを停止する必要があります。

このコードでは、millis()関数を使用して現在の時間とWDT変数の値の差を計算し、TIMEOUT定数で設定された時間（10秒）よりも大きい場合に、disableSteppers変数をHIGHに設定しています。その後、この変数はステッパーモーターを無効にするために使用されます。このようにして、プログラムの実行時間が長くなっても、ステッピングモータが安全に制御できるようにします。

```c++
if (millis() - WDT >= TIMEOUT)
  {
    disableSteppers = HIGH; 
  }
```

次に、Enピンを出力モードに設定します。

```c++
 pinMode(enPin, OUTPUT);
```

MIDI ライブラリの初期化を行います。MIDI.begin() 関数は、MIDI 通信を開始し、MIDI_CHANNEL_OMNI を指定して、すべての MIDI チャンネルからの MIDI イベントを受信するように設定します。また、MIDI.setHandleNoteOn() 関数と MIDI.setHandleNoteOff() 関数は、ノートオンイベントとノートオフイベントを処理するためのコールバック関数を設定します。

```c++
  MIDI.begin(MIDI_CHANNEL_OMNI); 
  MIDI.setHandleNoteOn(handleNoteOn); 
  MIDI.setHandleNoteOff(handleNoteOff); 
```

次にvoid loop()内の処理を見ていきます。
このプログラムは、MIDIからの入力に応じてステッピングモーターを制御し、タイムアウトが発生した場合はモータを無効にする処理となっております。

MIDI.read() 関数を使用して、MIDIデータを読み取ります。

```c++
  MIDI.read(); 
```

digitalWrite()関数を使用して、enPinピンの状態をdisableSteppers変数によって制御します。

```c++
  digitalWrite(enPin, disableSteppers); 
```

singleStep()関数を使用して、3つのステッピングモーターを制御します。
singleStep()関数はこの下で説明しております。

```c++
  singleStep(1, stepPin_M1); 
  singleStep(2, stepPin_M2);
  singleStep(3, stepPin_M3);
```

millis() 関数を使用して、WDT変数を更新します。
TIMEOUT(10秒)を過ぎた場合、disableSteppers変数をHIGHに設定して、モータを無効にします。

```c++
  if (millis() - WDT >= TIMEOUT)
  {
    disableSteppers = HIGH; 
  }
```

handleNoteOn関数は、MIDI信号のノートオンメッセージを処理するためのハンドラーです。モータを有効にするために disableSteppers 変数を LOW に設定し、pitchVals 配列からピッチ値を取得して motorSpeeds 配列の対応するチャンネルに保存します。ピッチ値は、MIDIノート番号に対応しています。

```c++
void handleNoteOn(byte channel, byte pitch, byte velocity) 
{
  disableSteppers = LOW; 
  motorSpeeds[channel] = pitchVals[pitch]; 
}
```

この関数は、MIDIノートオフコマンドが受信されたときに呼び出されます。MIDIノートオフコマンドは、MIDIノートオンコマンドとは対照的に、特定のノートが放されたことを示します。この関数では、引数として渡されたチャンネルとピッチのインデックスを使用して、ステッピングモータの速度を0に設定します。これにより、ノートオフコマンドが送信されたときに、モーターが停止するようになります。

```c++
void handleNoteOff(byte channel, byte pitch, byte velocity) 
{
  motorSpeeds[channel] = 0; 
}
```

singleStep()関数で1つのステッピングモーターを1ステップだけ動かします。関数には2つの引数があります。motorNumはモータの番号を、stepPinはステップパルスを生成するピン番号を指定します。

関数内部では、現在の時間（マイクロ秒単位）と前回のステップからの経過時間を比較して、指定された速度に達したかどうかを確認します。速度に達している場合は、前回のステップ時間に速度を加算し、ウォッチドッグタイマーをリセットし、ステップパルスを生成します。ステップパルスは、ステップピンをHIGHに設定してからLOWに戻すことによって生成されます。ステップパルスを生成することで、モータは1ステップ進みます。

```c++
void singleStep(byte motorNum, byte stepPin)
{
  if ((micros() - prevStepMicros[motorNum] >= motorSpeeds[motorNum]) && (motorSpeeds[motorNum] != 0)) 
  { 
    prevStepMicros[motorNum] += motorSpeeds[motorNum];
    WDT = millis(); 
    digitalWrite(stepPin, HIGH);
    digitalWrite(stepPin, LOW);
  }
}
```


## Domino(MIDI音楽ソフト)と連動させる

Dominoを起動させると以下のようが画面が表示されます。

![](../images/prototype/prototype1/domino/domino_1.jpg#center)

「ファイル」=>「環境設定」を開きます。

![](../images/prototype/prototype1/domino/domino_2.jpg#center)

「MIDI-OUT」の項目で「ポート A」「MIDI OUT デバイス」に「MIDI Stepper」を選択します。

![](../images/prototype/prototype1/domino/domino_3.jpg#center)

「トラックセレクトペインの表示切り替え」を選択すると左側にチャンネル設定の表示がされます。

![](../images/prototype/prototype1/domino/domino_5.jpg#center)

今回は3つのステッピングモーターを使用しており、それぞれA-01, A-02, A-03の3つのチャンネルで制御しております。

![](../images/prototype/prototype1/domino/domino_6.jpg#center)

## ボディ設計

- プロトタイプ1

以下のようにFusion360でモデリングしました。

  - ステッピングモーターの音を拡張させるため、入口が細く、出口が広がっていく構造を作成しました。

![](../images/prototype/prototype2/fusion360/fusion360_1.jpg#center)

![](../images/prototype/prototype2/fusion360/fusion360_2.jpg#center)

2.5mmの薄いMDFを敷いてその上にステッピングモーターを置いて振動音を増幅させるように作成しました。

![](../images/prototype/prototype2/fusion360/fusion360_3.jpg#center)

レーザー加工機でパーツをカットしました。

![](../images/prototype/prototype2/laser/laser_1.jpg#center)

レーザー加工機で以下のようにMDF5.5mmを重ね合わせてスピーカーを作成し、MDF2.5mmの板にステッピングモーターを固定しました。

![](../images/prototype/prototype2/laser/laser_2.jpg#center)

この構造ではステッピングモーターの板を薄くすることでより振動をより伝えることができると考えましたが、大きな効果はありませんでした。

- プロトタイプ2
  - 

## パッケージング

### 評価

- 

### チェックリスト

- 本プロトタイプはモータの振動による音を利用した楽器を作成できている
- 対象者が容易に楽器を使用することができる


## データ

- [fusion360 speacker prototype data](../files/prototype2/speacker-prototype1.f3d)

- [speacker prototype lasercut data](../files/prototype2/speacker.pdf)

## 部品
|    部品    | 販売先 |  金額 | 必要個数 |   合計金額   | リンク |
| ----------- | ------ | ---- | ------ | ---------- | ----- |
| Arduino Uno R3 | switch science | ¥3,960 | 1個 | ¥3,960 | [Link](https://www.switch-science.com/products/789) |
| CNC Shield | Amazon | ¥689 |   1個   |    ￥689     | [Link](https://www.amazon.co.jp/KKHMF-%E6%8B%A1%E5%BC%B5%E3%83%9C%E3%83%BC%E3%83%89-A4988-%E3%83%89%E3%83%A9%E3%82%A4%E3%83%90%E3%83%BC%E3%83%9C%E3%83%BC%E3%83%89-Arduino%E3%81%A8%E4%BA%92%E6%8F%9B/dp/B088FLTQ2K/ref=asc_df_B088FLTQ2K/?tag=jpgo-22&linkCode=df0&hvadid=589001997787&hvpos=&hvnetw=g&hvrand=3902595438584672939&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=1009052&hvtargid=pla-1655365521301&psc=1) |
| ステッピングモータ(Stepper Motor) Nema 17 Bipolar 40mm | Amazon | ￥3,378 | 3個 | ￥10134 | [Link](https://www.amazon.co.jp/%E3%82%B9%E3%83%86%E3%83%83%E3%83%94%E3%83%B3%E3%82%B0%E3%83%A2%E3%83%BC%E3%82%BF-Stepper-Motor-Bipolar-Printer/dp/B00PNEQI7W?th=1) |
| DRV8825 4層 ステッパ モータ ドライバ モジュール | Amazon | ￥180 | 3個 | ￥540 | [Link](https://www.amazon.co.jp/KOZEEY-%E3%83%92%E3%83%BC%E3%83%88%E3%82%B7%E3%83%B3%E3%82%AF%E4%BB%98%E3%81%8D-DRV8825-4%E5%B1%A4-%E3%82%B9%E3%83%86%E3%83%83%E3%83%91-%E3%83%A2%E3%83%BC%E3%82%BF-%E3%83%89%E3%83%A9%E3%82%A4%E3%83%90-%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB-3D%E3%83%97%E3%83%AA%E3%83%B3%E3%82%BF%E5%AF%BE%E5%BF%9C-%E4%BA%A4%E6%8F%9B%E6%80%A7-%E4%BE%BF%E5%88%A9%E6%80%A7-%E9%AB%98%E6%80%A7%E8%83%BD-%E5%AE%89%E5%AE%9A%E6%80%A7/dp/B075462FBC) |
| AC to DC 12V 2A アダプター | Amazon | ￥1,398 | 1個 | ￥1,398 | [Link](https://www.amazon.co.jp/%E6%B1%8E%E7%94%A8AC%E3%82%A2%E3%83%80%E3%83%97%E3%82%BF%E3%83%BC-%E6%9C%80%E5%A4%A7%E5%87%BA%E5%8A%9B24W-%E3%82%B9%E3%82%A4%E3%83%83%E3%83%81%E3%83%B3%E3%82%B0%E5%BC%8F-%E9%9B%BB%E6%BA%90%E3%82%A2%E3%83%80%E3%83%97%E3%82%BF%E3%83%BC-%E3%83%86%E3%83%BC%E3%83%97%E3%83%A9%E3%82%A4%E3%83%88/dp/B07D74RXPK/ref=sr_1_2_sspa?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=HIZU9BZDZHOH&keywords=ac+to+ac%E3%82%A2%E3%83%80%E3%83%97%E3%82%BF%E3%83%BC+12v+2a&qid=1677467574&sprefix=ac+to+d%E3%82%A2%E3%83%80%E3%83%97%E3%82%BF%E3%83%BC+12v+2a%2Caps%2C219&sr=8-2-spons&psc=1&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUFDWVY5UTc5R1lGQzImZW5jcnlwdGVkSWQ9QTAzMjM1MzgzQlgwV1BLOFlURklXJmVuY3J5cHRlZEFkSWQ9QTI5TTZBTkVWMkk4Wlcmd2lkZ2V0TmFtZT1zcF9hdGYmYWN0aW9uPWNsaWNrUmVkaXJlY3QmZG9Ob3RMb2dDbGljaz10cnVl) |


## インストールするソフト・ライブラリ一覧

- [FLIP](https://www.microchip.com/en-us/development-tool/flip#)

- [mocolufa](https://github.com/kuwatay/mocolufa)

- [Arduino-MIDI-Stepper-Motor-Instrument](https://github.com/jzkmath/Arduino-MIDI-Stepper-Motor-Instrument)

- [Domino](https://takabosoft.com/domino)


## 参考資料
- [ステッピングモーターの概要と特徴](https://www.orientalmotor.co.jp/products/stepping/overview_1/)

- [FORUM Arduino(CNC shield V3 A4988 Motors Z and A not move)](https://forum.arduino.cc/t/cnc-shield-v3-a4988-motors-z-and-a-not-move/484088)

- [【Arduino】Arduino UNOをMIDIコントローラにする](https://monorepi.jp/archives/1404)

- [Arduino MIDI Stepper Synth](https://www.hackster.io/JonJonKayne/arduino-midi-stepper-synth-d291ae)

- [Arduino-MIDI-Stepper-Motor-Instrument](https://github.com/jzkmath/Arduino-MIDI-Stepper-Motor-Instrument)

- [Moco for LUFA](https://webmusicdevelopers.appspot.com/codelabs/arduino-mocolufa/index.html?ja-jp#5)

- ["楽器"としてのステッピングモーター入門](https://tlo-olb.hatenablog.com/entry/2019/03/22/191524)

- [MIDIノート番号](https://www.asahi-net.or.jp/~hb9t-ktd/music/Japan/Research/DTM/freq_map.html)

- [MIDIのデータ構造](http://www1.plala.or.jp/yuuto/midi/p0200.html#:~:text=MIDI%20%E3%81%A7%E3%81%AF%E3%80%81%E6%BC%94%E5%A5%8F%E6%83%85%E5%A0%B1%E3%82%92,%E3%81%A6%E9%80%81%E3%82%89%E3%82%8C%E3%82%8B%E3%82%8F%E3%81%91%E3%81%A7%E3%81%99%E3%80%82)

- [Arduino MIDI Library の使い方](https://qiita.com/yudai220/items/3bde9461f282d56d1ac2)

- [DRV8825 Stepper Motor Driver Carrier, High Current](https://www.pololu.com/product/2133)

- [Setting the Current Limit on Pololu Stepper Motor Driver Carriers](https://www.youtube.com/watch?v=89BHS9hfSUk)

- [Drv8825 Stepper Motor Driver Arduino Tutorial](https://lastminuteengineers.com/drv8825-stepper-motor-driver-arduino-tutorial/)

- [CNCシールドを流用するにあたってのメモ](https://cottonmachine-remodeling.blogspot.com/2018/05/cnc.html)

- [Arduino CNC Shield – 100% GRBL Compatable](https://blog.protoneer.co.nz/arduino-cnc-shield/)

