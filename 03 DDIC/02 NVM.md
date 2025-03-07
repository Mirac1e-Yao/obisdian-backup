# 基本概念
---
- OTP NVM：One Time Programmable，一次性可编程存储器
- NVM：Non Volatile Memory，非易失性存储器，包括Flash、EEPROM、Mask ROM、RRAM等
- VM：易失性存储器，包括RAM、Register、Linebuffer、SRAM、DRAM等
- Register：寄存器，属于高速存储器，可以存储设备的配置状态和控制信号

# 工作原理
---
- 编程（Write）过程：通过特殊的高电压脉冲来写入数据以改变存储单元的物理特性。
- 读取（Read）过程：通过施加较低的电压，用于判断电荷是否存在来判断存储单元的状态。

# 应用流程
---
- 由于OLED DDIC的寄存器数目较多并且需求一定的烧录次数，因此会需要较大size的NVM，那意味是面积会变大，所以使用OTP Cell + Flash的方案性价比更高。
- Chipone的烧录NVM时分为三个部分：
	- Auto：该模式下会自动将寄存器搬运至对应的NVM地址上，地址映射是固定的。通常放在项目阶段烧录，烧录的内容是对应项目的配置代码。
	- Manual：此模式需要手动的填写NVM地址，地址映射需要手动配置。通常放在CP阶段烧录，烧录的内容是trim code、id 及 各个寄存器的default值等
	- Flash：可能会通过逻辑判断(ex：写入计数器)NVM的写入次数是否用尽，达到阈值时会通过SPI将原先要烧录进NVM的寄存器值**依照设计的地址映射规则**传输至Flash中。

## 什么是Margin Read？

- 基于NVM读取原理，是通过加压来存储单元的状态，在debug时通过设定不同的读取电压(称为Margin电压)来排查OTP读取内容与预期不符合的情况。