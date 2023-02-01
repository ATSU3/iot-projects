# Raspberry Pi

## GPIO

GPIOとは汎用入出力(General Purpose Input/Output)を意味しており、RaspberryPiに搭載されている信号ピンのことで40本のピンがあります。

![](../../../images/prototype/prototype1/circuit/circuit_4.jpg#center)

### GPIO x Python 

RaspberryPiを用いてPythonでGPIOを使用するには
[RPi.GPIO](https://pypi.org/project/RPi.GPIO/)パッケージを利用します。

```python
import RPi.GPIO as GPIO
```

GPIOのピン番号指定を「BCM」に設定します。

GPIO.setmodeの引数には、GPIO.BOARD、GPIO.BCMの2種類が選択できますが、GPIO.BOARDを選択した場合、ピン番号がピンの配置の順番で番号が割り振られますが、GPIO.BCMの場合、GPIOの数字での指定となります。
```python
GPIO.setmode(GPIO.BCM)
```

以下の記述をすることでGPIOピンの値を上書きされた場合に表示される警告を無視することができます。
```python
GPIO.setwarnings(False)
```

setup()の第1引数にはチャンネル(ピン番号)を入れます。(BOARDまたはBCM)に基づくチャンネル番号です)。

第2引数には、GPIO.IN(入力として設定)もしくは、GPIO.OUT(出力として設定)します。

第3引数にはpull_up_downのキーワード引数を用いて、GPIO.PUD_UP(プルアップ)、またはGPIO.PUD_DOWN(プルダウン)をRaspberryPi内部の抵抗を利用して実装できます。

```python
GPIO.setup(2, GPIO.IN, pull_up_down=GPIO.PUD_UP)
```

!!!note
    入力ピンが何かに接続されていない場合、「浮動」します。つまり、ボタンやスイッチを押すまでは何も接続されていないので、読み込まれる値は未定義です。おそらく、主電源の干渉を受けると、値も変わってしまいます。

    この問題を回避するために、プルアップ抵抗やプルダウン抵抗を使用します。このようにして、入力のデフォルト値を設定することができます。プルアップ/ダウン抵抗は、ハードウェア側からでもソフトウェア側からでも対応可能です。ハードウェアでは、入力チャネルと3.3V（プルアップ）または0V（プルダウン）の間に10Kの抵抗を使用するのが一般的です。RPi.GPIOモジュールを使えば、Broadcom SOCがソフトウェアでこれを行うように設定することができます。


こちらの記述はGPIO2ピンの瞬間の入力がLOW(0)の時という条件分になりますが、GPIO.input()の引数にGPIOピン番号を入れるとある瞬間の入力を取得できます。
```
if GPIO.input(2) == GPIO.LOW
```


## 参考資料
- [raspberry-gpio-python Wiki](https://sourceforge.net/p/raspberry-gpio-python/wiki/BasicUsage/)


