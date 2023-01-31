# Raspberry Pi

## GPIO

GPIOとは汎用入出力(General Purpose Input/Output)を意味しており、RaspberryPiに搭載されている信号ピンのことで40本のピンがあります。

![](../../../images/prototype/prototype1/circuit/circuit_4.jpg#center)

### GPIO x Python 

RaspberryPiを用いてPythonでGPIOを使用するには
[RPi.GPIO](https://pypi.org/project/RPi.GPIO/)パッケージを利用します。

```
import RPi.GPIO as GPIO
```

GPIOのピン番号指定を「BCM」に設定します。
```
GPIO.setmode(GPIO.BCM)
```

GPIO.setmodeの引数には、GPIO.BOARD、GPIO.BCMの2種類が選択できますが、GPIO.BOARDを選択した場合、ピン番号がピンの配置の順番で番号が割り振られますが、GPIO.BCMの場合、GPIOの数字での指定となります。


