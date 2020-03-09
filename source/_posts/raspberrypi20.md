---
title: python 3.X 对 ADS1115 的操作
date: 2019-01-16 20:12:10
categories:
- raspberry
tags:
- 树莓派
- ADS1115
---
我前面写的文章都是用 python2.7 对 ADS1115 进行操作，但是，我们都知道 python3.X 才是王道，所以，这次我用的是 3.X 的库。

<!--more-->

## 参考资料

[Adafruit 4-Channel ADC Breakouts](https://learn.adafruit.com/adafruit-4-channel-adc-breakouts?view=all)

[ Adafruit CIRCUITPYTHON_ADS1X15 Library](https://circuitpython.readthedocs.io/projects/ads1x15/en/latest/index.html)

[Adafruit CircuitPython API Reference](https://circuitpython.readthedocs.io/en/3.x/docs/)

{% codeblock %}

[SKU:DFR0553I2C 16-Bit ADC Module](http://wiki.dfrobot.com.cn/index.php?title=(SKU:DFR0553)I2C_16-Bit_ADC_Module)

{% endcodeblock %}

因为格式原因，所以，就直接将网址 pull 到浏览器上就好了。

## 操作步骤

[Adafruit_CircuitPython_ADS1x15](https://github.com/adafruit/Adafruit_CircuitPython_ADS1x15)

在树莓派中输入

	sudo pip3 install adafruit-circuitpython-ads1x15
	
然后就是连线，还是延续上面的连线。

![](/images/raspberry/18_0.png)

我写的代码如下。

{% codeblock %}
import time
import board
import busio
import adafruit_ads1x15.ads1115 as ADS
from adafruit_ads1x15.analog_in import AnalogIn

# Create the I2C bus
i2c = busio.I2C(board.SCL, board.SDA)

# Create the ADC object using the I2C bus
ads0 = ADS.ADS1115(i2c)
ads1 = ADS.ADS1115(i2c, address = 73)

# Create single-ended input on channel 0
chan0 = AnalogIn(ads0, ADS.P0)
chan1 = AnalogIn(ads0, ADS.P1)
chan2 = AnalogIn(ads0, ADS.P2)
chan3 = AnalogIn(ads0, ADS.P3)

chan4 = AnalogIn(ads1, ADS.P0)
chan5 = AnalogIn(ads1, ADS.P1)
chan6 = AnalogIn(ads1, ADS.P2)
chan7 = AnalogIn(ads1, ADS.P3)

# Create differential input between channel 0 and 1
#chan = AnalogIn(ads, ADS.P0, ADS.P1)

print("{:>5}\t{:>5}".format('raw', 'v'))

while True:
    print("{:>5} | {:>5} |{:>5} |{:>5} |{:>5} |{:>5} |{:>5} |{:>5}".format(chan0.value, chan1.value,chan2.value,chan3.value,chan4.value,chan5.value,chan6.value,chan7.value))
    print("{:>5.3f} | {:>5.3f} | {:>5.3f} |{:>5.3f} |{:>5.3f} |{:>5.3f} |{:>5.3f} |{:>5.3f} |".format(chan0.voltage,chan1.voltage,chan2.voltage,chan3.voltage,chan4.voltage,chan5.voltage,chan6.voltage,chan7.voltage))
    time.sleep(0.5)
{% endcodeblock %}

其输入如下：

![](/images/raspberry/19_4.png)

额，我这个图因为接了底线所以是这样输出的。















