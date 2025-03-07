# 基本概念
---
- 这个模块主要是为芯片提供晶振，这个频率会作为基准频率在分频后给各个电路模块使用；
- 客户通常会询问OSC支持的trim step、精度及范围，**通常会提供较宽松的版本给内部留空间，需要找PM对齐**；
- Trim Step：Trim Code 每档对OSC频率的调整精度；
- 范围：OSC频率可支持的范围，**涉及客户评估项目频点**；
- 精度：OSC中心频率受温度，以及不同IC间的波动范围，**需要区分是否有开追频**；
- 验证成功了吗
## 多频点在应用上有什么差别？

- 在分多个OSC的情况下，**通常是考量了功耗因素，特别是有datapath与dsc频点单独设的方案**
- 下图以ICNA3508A的OSC架构图为例：
	- MIPI DSI LP-RX/HS-RX部分是使用D-PHY的CLK，即外部OSC；
	- MIPI DSI LP-TX/REG-RW 以及PIC/BC/Charge Pump部分的控制是使用OSC0频点，默认为60MHz；
	- DSC则使用OSC2/OSC1，默认为150/128 MHz，Datapath使用OSC1，默认为128MHz
![[ICNA3508A_OSC.png]]
## 如何计算OSC是否满足需要？

- Chipone计算公式如下：
	- OSC1 = 横向分辨率 * 纵向分辨率 * 帧率 * (1+Porch区比例) / 4 / 1000000
	- OSC2 = 横向分辨率 * 纵向分辨率 * 帧率 * (1+Porch区比例) / 3 / 1000000
	- **Porch区比例通常取20%**
	- **/4 是因为Datapath 1个CLK处理4笔Pixel Data，即1T4P**
	- **/3 是因为DSC encoder为3倍压缩，所以接收的数据量减少3倍**
- 以2400 x 1080，120Hz为例：
	- **Pixel Clock = 2400 x 1080 x 120 = 311,040,000 Hz**
	- OSC1 = Pixel Clock * ( 1 + 20% ) / 4 / 1000000 = 93.312Mhz
	- OSC2 = Pixel Clock * ( 1 + 20% ) / 3 / 1000000 = 124.416Mhz
	- MIPI BitClock = Pixel Clock * 3(RGB) * 8(bit) / 4(lane) = 1,866,240,000 bps
	- MIPI ByteClock = MIPI Bit Clock / 8 = 233,280,000 Bps
	- 1 UI = 1 / BitClock
	- DSI Clock = MIPI BitClock / 2，**因为是一个Clock传输2bit数据(上升沿和下降沿双采样)**
- 依据外部MIPI BitClock(4lane)可知，最终DIC需要在1s内处理7,464,960,000 bit 的数据，按照1T4P的架构来处理的话，即1个时钟周期处理4个Pixel的数据(共计96bit)，可知主频至少需要77,760,000 Hz，按照x1.2作为margin，则建议使用93,312,000 Hz。

## 如何计算IC内频？

- DIC在Datapath上处理时使用的是PCLK，当来到TCON时，会切换为DCLK。通过数RTN设定数目的DCLK，实现行时的设定。注意使用video mode时，**内频需要比外频跑的快，否则会出异常**。


## 为什么PCLK一般是DCLK的2倍

- 首先，PCLK是在Datapath上使用的，DCLK是在TCON上使用的。然后因为DIC通常分为左右Source Driver去接收前端Datapath过来的数据，相当于硬件电路上决定了后级电路在读取数据时是并行处理的，所以设计上通常就不会预留PCLK=DCLK的选项，避免主频加快对性能没有提升反而增加了功耗。
- 换言之，PCLK为DCLK的2倍，是一种基于硬件综合考量后的结果。