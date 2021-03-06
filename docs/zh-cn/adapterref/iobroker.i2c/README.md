---
translatedFrom: en
translatedWarning: 如果您想编辑此文档，请删除“translatedFrom”字段，否则此文档将再次自动翻译
editLink: https://github.com/ioBroker/ioBroker.docs/edit/master/docs/zh-cn/adapterref/iobroker.i2c/README.md
title: 用于I2C的ioBroker适配器
hash: 9yLvFVolYnohYVz2xeO0SFDxNSYS0MgRquEsMgvBf88=
---
![安装数量](http://iobroker.live/badges/i2c-stable.svg)
![NPM版本](http://img.shields.io/npm/v/iobroker.i2c.svg)
![资料下载](https://img.shields.io/npm/dm/iobroker.i2c.svg)
![特拉维斯](https://img.shields.io/travis/UncleSamSwiss/ioBroker.i2c.svg)
![GitHub问题](https://img.shields.io/github/issues/UncleSamSwiss/ioBroker.i2c.svg)

*** **重要更新**

此适配器的开发将于2020年8月重新开始。敬请关注！

我将首先解决最紧迫的问题，然后再发布新的主要版本，使适配器达到最新的开发标准。
***

![I2C徽标](../../../en/adapterref/iobroker.i2c/admin/i2c.png)

＃用于I2C的ioBroker适配器
使用I2C总线与连接到本地系统的设备进行通信。

该适配器应该可以在Raspberry Pi，C.H.I.P.，BeagleBone或Intel Edison等Linux板上使用。

##安装
在安装之前，请阅读[i2c总线模块的安装指南](https://www.npmjs.com/package/i2c-bus#installation)。

尤其要确保已在系统上正确配置并启用了I2C（如果需要）：

* [在Raspberry Pi上配置I2C]（https://github.com/fivdi/i2c-bus/blob/master/doc/raspberry-pi-i2c.md）
* [在英特尔Edison Arduino主板上配置I2C]（https://github.com/fivdi/i2c-bus/blob/master/doc/edison-adruino-base-board-i2c.md）

启用并配置I2C之后，可以通过ioBroker Admin安装此适配器：

1.启动适配器（必须运行适配器才能使发现起作用）
2.打开实例配置对话框
3.按下“搜索设备”按钮以发现所有已连接的I2C设备-这需要一些时间，请耐心等待！
4.在各自的选项卡中配置所有找到的设备。
5.保存配置（这将重新启动适配器）

###访问权限问题
根据ioBroker安装的年龄，`iobroker`用户（或在ioBroker运行的任何用户下）可能没有访问I2C的适当权限。

如果已连接设备，但未在配置屏幕中显示该设备，请确保将用户添加到`i2c`组：

```sh
sudo usermod -G i2c iobroker
```

注意：如果没有标准安装，请用运行ioBroker安装的用户替换上述命令中的`iobroker`（检查`ps`）。

##配置
###巴士号码
这是要打开的I2C总线/适配器的编号，/ dev / i2c-0为0，/ dev / i2c-1为1，...

在Raspberry Pi 3和4B上，该值为“ 1”。

##支持的设备
当前支持以下设备。括号中的数字是十六进制格式的设备的已知地址（无读取位）。

### ADS1015（48-4B）
德州仪器（TI）具有内部基准的4个3.3kSPS，12位ADC。

### ADS1115（48-4B）
德州仪器（TI）4x 860-SPS，16位ADC，具有内部基准。

### BME280（76，77）
博世数字湿度，压力和温度传感器。

### MCP23008 8位I / O扩展器（20-27）
具有串行接口的Microchip 8位I / O扩展器。

### MCP23017 16位I / O扩展器（20-27）
具有串行接口的Microchip 16位I / O扩展器。

### PCF8574 8位I / O扩展器（20-27）
德州仪器（TI）用于I2C总线的远程8位I / O扩展器。

### PCF8574A 8位I / O扩展器（38-3F）
德州仪器（TI）用于I2C总线的远程8位I / O扩展器。

### PCA9685 16通道12位PWM伺服/ LED驱动器（40-7F）
Adafruit PCA9685转接板，用于16通道12位PWM。适配器专注于使用0..4095中的16个通道作为LED调光器。
当PWM（和GND）连接到N通道Mosfet模块时，可以驱动许多LED，例如基于D4184。将LED GND连接至MOSFET，并将+ 12/24 / n V连接至PSU。

###通用设备（03-77）
通用I2C设备。可以根据硬件配置寄存器。

##脚本中的用法
脚本中的`sendTo`支持的命令是`search`，`read`和`write`。

`search`将总线编号作为消息，并返回总线上找到的地址数组的JSON字符串。

`read`将包含地址和可选寄存器以及要读取的字节数的对象作为消息。它返回包含读取数据的缓冲区。

`write`将包含地址，数据作为缓冲区和可选写入寄存器的对象作为消息。成功时将返回写入缓冲区。

###脚本用法示例
```js
sendTo('i2c.0', 'search', 1, (ret) => {
    log('Ret: ' + ret, 'info');
});

sendTo('i2c.0', 'read', {
    address: 0x40,
    register: 0x02,
    bytes: 2
}, (ret) => {
    log('Ret: ' + ret.inspect(), 'info');
});

sendTo('i2c.0', 'write', {
    address: 0x40,
    register: 0x00,
    data: Buffer.from([0x44, 0x27])
}, (ret) => {
    log('Ret: ' + ret.inspect(), 'info');
});
```

##相容性
兼容性已经过Raspberry Pi 3和4B的测试。

##错误报告和功能请求
请使用GitHub存储库报告任何错误或请求新功能。

如果您需要缺少设备，请提供适配器配置中报告的IC类型（品牌，型号，...）及其地址。

＃＃ 谢谢
该项目基于[i2c总线](https://www.npmjs.com/package/i2c-bus)NPM模块。感谢fivdi的出色模块！

##第三方许可
### BME280
BME280代码基于https://github.com/skylarstein/bme280-sensor：

麻省理工学院执照

版权所有（c）2016 Skylar Stein

特此免费授予获得此软件和相关文档文件（“软件”）副本的任何人无限制地处理软件的权利，包括但不限于使用，复制，修改，合并的权利，发布，分发，再许可和/或出售本软件的副本，并允许具备软件的人员这样做，但须满足以下条件：

以上版权声明和本许可声明应包含在本软件的所有副本或大部分内容中。

本软件按“原样”提供，不提供任何形式的明示或暗示担保，包括但不限于对适销性，特定目的的适用性和非侵权性的保证。无论是由于软件，使用或其他方式产生的，与之有关或与之有关的合同，侵权或其他形式的任何索赔，损害或其他责任，作者或版权所有者概不负责。软件。

### ADS1x15
ADS1x15代码基于https://github.com/alphacharlie/node-ads1x15/blob/master/index.js

node-ads1x15本身基于https://github.com/adafruit/Adafruit_Python_ADS1x15

MIT许可证（MIT）

版权所有（c）2016 Adafruit Industries

特此免费授予获得此软件和相关文档文件（“软件”）副本的任何人无限制地处理软件的权利，包括但不限于使用，复制，修改，合并的权利，发布，分发，再许可和/或出售本软件的副本，并允许具备软件的人员这样做，但须满足以下条件：

以上版权声明和本许可声明应包含在本软件的所有副本或大部分内容中。

本软件按“原样”提供，不提供任何形式的明示或暗示担保，包括但不限于对适销性，特定目的的适用性和非侵权性的保证。无论是由于软件，使用或其他方式产生的，与之有关或与之有关的合同，侵权或其他形式的任何索赔，损害或其他责任，作者或版权所有者概不负责。软件。

### PCA9685
PCA9685代码基于https://github.com/adafruit/Adafruit_Python_PCA9685/blob/master/Adafruit_PCA968/PCA9685.py

MIT许可证（MIT）

版权所有（c）2016 Adafruit Industries作者：Tony DiCola

特此免费授予获得此软件和相关文档文件（“软件”）副本的任何人无限制地处理软件的权利，包括但不限于使用，复制，修改，合并的权利，发布，分发，再许可和/或出售本软件的副本，并允许具备软件的人员这样做，但须满足以下条件：

以上版权声明和本许可声明应包含在本软件的所有副本或大部分内容中。

本软件按“原样”提供，不提供任何形式的明示或暗示担保，包括但不限于对适销性，特定目的的适用性和非侵权性的保证。无论是由于软件，使用或其他方式产生的，与之有关或与之有关的合同，侵权或其他形式的任何索赔，损害或其他责任，作者或版权所有者概不负责。软件。

也基于：https://github.com/tessel/servo-pca9685/blob/master/index.js

版权所有2014 Technical Machine，Inc.请参阅此发行版顶级目录中的COPYRIGHT文件。

根据Apache许可证2.0版<LICENSE-APACHE或http://www.apache.org/licenses/LICENSE-2.0>或MIT许可证<LICENSE-MIT或http://opensource.org/licenses/MIT>许可，由您选择。除非按照这些条款，否则不得复制，修改或分发此文件。

## Changelog

### 0.0.8 (2020-05-26)
* (Peter Müller) Added support for Generic device.
* (Peter Müller) Added support for `read` and `write` commands in scripts using `sendTo`.
* (Peter Müller) Added support for interrupts on PCF8574, MCP23008, MCP23017 devices.

### 0.0.7 (2020-01-19)
* (CC1337) Added support for PCA9685.

### 0.0.6 (2019-03-17)
* (UncleSamSwiss) Added support for BME280.
* (UncleSamSwiss) Added support for ADS1015 / ADS1115.

### 0.0.5 (2019-01-12)
* (UncleSamSwiss) Added support for MCP23008.

### 0.0.4 (2018-07-23)
* (UncleSamSwiss) Improved stability of MCP23017.
* (Apollon77) Latest ioBroker utils and testing including node 10.

### 0.0.3 (2017-11-12)
* (UncleSamSwiss) Added support for MCP23017.

### 0.0.2 (2017-07-30)
* (UncleSamSwiss) Added support for inverting PCF8574 inputs and outputs.

### 0.0.1 (2017-07-27)
* (UncleSamSwiss) Initial version