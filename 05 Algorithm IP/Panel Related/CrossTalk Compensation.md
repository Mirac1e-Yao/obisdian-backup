# 基本概念
---
- CrossTalk Compensation，简称CTC。
- Average Pixel Level，简称APL，平均像素灰阶。
- Look Up Table，简称LUT，查找表。
- Frame Rate，简称FR，帧率。
- Display Brightness Value，简称DBV，显示亮度值。
- On Pixel Ratio，简称OPR，实际显示像素占比。
- Gray Level，简称GL，灰阶。
- Indium Tin Oxide，简称ITO，氧化铟锡薄膜。
## 为什么要做CTC ？
---
- 如下图所示，Source Line方向上画面灰阶由低灰阶向高灰阶发生跳变，实际电路上行为是Vdata由低电压向高电压跳变。由于耦合效应的影响，使得Gate Line方向上的ELVDD电压被抬起，出现亮线异常。同理，当ELVDD电压被拉低，会出现暗线异常。
![[Crosstalk 成因.png]]
## 为什么不是ELVSS受到影响而引起的？
---
- 在AMOLED的结构中，ELVDD通常是在ITO阳极上走线来给每一行的子像素电路供电的，而非像ELVSS是供电于整块金属阴极上，是所有子像素电路共用的，且依据电流公式可知，ELVDD对亮度影响较大。
![[AMOLED结构图.png]]


# 补偿原理
---
- 可以通过Linebuffer来对行灰阶数据进行缓存，计算行平均灰阶数据来完成行灰阶数据之间的比对，以此完成对补偿位置的识别。然后对灰阶数据变化量进行判定，来选用已经配置在IC中的补偿值LUT，完成补偿。
## Chipone 架构举例
---
- 数据进来后会先进行灰阶数据到电压数据的Remap，此举可理解为完成从Data域到Gamma域的转换；然后，会将当前行数据的平均值APL放入Linebuffer中，通过选择与下一行or上一行进行比较来得出变化量，到预先配置好的LUT里查找到对应的补偿值Offset。完成之后，因为架构中会补在连续的8行数据上，所以需要对每一行进行权重配置（Weighting），防止过补的情况发生。最后再经过Offset Mask，来对一些不需要补偿的情况做过滤再输出。
![[Chipone CTC 架构.png]]


# 应用场景
---
- 在实际应用时会有很多分块场景的情况出现，像是按钮，iPhone Home键等，因此属于比较关键的算法，在mux一对多的面板中crosstalk现象尤为严重，通常都需要此IP开启；
- 效果会依据各家面板厂或终端厂商所提供的killer pattern来确认视效；
## 如何调试
---
- Chipone 的IP可以先以钢琴键图，来模拟各种APL变化量大小的情况，然后手调补偿值，确定此画面在不同的FR/DBV/GL/OPR/killer pattern等条件下皆OK，则可作为公版。