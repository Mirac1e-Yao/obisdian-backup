# 基本概念
---
- SDL：Single Data Line，可理解为MUX 1:2
- DDL：Dual Data Line，可理解为MUX 2:4
- noMUX：即MUX 1:1
- eMUX：Embed MUX，即嵌入式MUX，将MUX电路从面板移至DIC内部

## 为什么会有SDL和DDL的接法?
---
- SDL接法的面板，RGBG的subpixel R/B与G1/G2分别共用一根MUX线，因为需要在同一根MUX线上进行电压切换，所以面板时序中Pixel Circuit 的充电时间受限(ex：SCK1)，**故导致了SDL面板在高刷新率的应用上容易出现充电时间不足而视效变差**
- 基于SDL面板的局限性，DDL面板将每个subpixel单独拉线，这种做法既降低了单线的容抗特性，面板时序上Pixel Circuit的充电时间也更有余量(ex：SCK1)，**所以高刷新率的应用更多是DDL面板
- 但因为DDL其实是同时对二行数据进行充电，即实际上一个SCK周期是完成2行pixel circuit的充电，此时再引入FIAA的设计会变得十分复杂，所以又出现了SDL/eMUX/noMUX + FIAA等方式

![[SDL与DDL的区别.png]]



# DDL面板的驱动时序
---
- 首先，基于GOA电路的工作原理可知，DIC输出GSTV/GCK触发电路启动，需要等到输出GCB时，GOA电路才完成真正意义上的向面板输出信号。
![[GOA_电路工作原理示意图.png]]
- 