# 基本概念
---
- De-mura：简称DMR。
- Charge Coupled Device：简称CCD，电荷耦合元件。
- sandy mura：沙状，也有称为黑雾(CSOT)，像素点级别的亮度不均，看起来整屏脏脏的不细腻。
## 为什么要做De-mura？

-  面板像素电路生产过程中因为**成膜厚度/晶化/刻蚀/清洗药液等制程环节出现不均**，导致各子像素驱动电路的TFT性能不均一，光学表现上出现色亮不均，影响良率。
- 为了提升良率，需要导入De-mura。
## 如何选择CCD相机？

- 针对2400x1080 RGBG面板，R&B subpixel的个数皆为1.296M，G subpixel的个数2.592M，因此需挑选大于该数目(Total=5.184M)分辨率的传感器。
- 以Radiant Y16-G举例，最小亮度支持到0.0005 cd/m²，最大亮度支持到10^10 cd/m²。
## Flash的存储方式与DIC的关系

- Flash存储空间由小到大按照 **页(Page)、扇区(Sector) 和 块(Block) 来进行区分**。DIC会将需要烧录到Flash的bin档内容**依据DIC寄存器表与Flash存储单元映射关系**来规定起始地址，需要避开MCS的部分。
- 1 Page = 256 Bytes
- 1 Sector = 16 Pages = 4096 Byte = 4K Byte
- 1 Block = 16 / 8 Sectors = 64K / 32K Byte
- 以16M bit (2M Byte) Flash为例，其含有32 Blocks，512 Sectors，8192 Page。

# 原理说明
---
- 通过拍照数据来获取所有子像素在拍照灰阶的亮度资讯后，可以通过灰阶-亮度公式计算出实际的灰阶以及Gamma；因为Tune Gamma时选在面板中心区域，所以便能认为该区域的亮度数据是准确的，将其作为Ideal Curve来给其他子像素参考。
- 其他子像素的Curve通过**Data_out=Data_in + Gain * Offset**的补偿模型来趋近，使得面板整体的均匀性获得提升。
- 各家的补偿模型公式会不一样，有多项式，二次式等。

![[Demura Compensation Flow.png]]
## 为什么应用时会分Plane进行补偿

- 为了使得最终呈现的补偿效果更好，良率更高。
- Plane相当于灰阶绑点，而不同灰阶的mura程度存在差异，尤其是高灰阶与低灰阶。如果只用1个Plane去补偿，那使用该offset去适配所有灰阶可能效果不好，因此至少分配2个Plane较为合理。
- 应用时Plane数目越多，由于Demura RAM大小恒定，那blocksize难免需要增大，这样又会使得sandy mura补得不好，所以需要依据实际状况进行取舍。
## Chipone 架构举例
![[Chipone DMR Flow.png]]
![[Chipone DMR1 Flow.png.png]]
- G2G Mapping：用于处理补偿后灰阶溢出的情况
# 应用流程
---
- 主要分为如下3个流程：
- 前两个流程主要在模组厂内完成，会有生产时间TT的限制。一般设计时会将Demura IP的补偿控制（Blocksize、补偿值、Gain LUT绑点等）**预留在REG中以便于工程调试**，待确定公版后此IP的全部内容会与offset一起烧录至Flash；
### 拍照

- 需要依据各灰阶mura形态选取LUT绑点灰阶
- 需要准备Image IP off List
- 若由屏厂做前处理，需要拉通算法做Tool及数据格式的对接
### 烧录

- offset会在PC端经**压缩算法(encoder)** 形成烧录bin档，经MIPI烧录进DIC的Demura RAM后再经SPI来传输进Flash，实现offset数据存储
- 需要确认项目的Flash规格(QE、Status Register)是否对应flow code
- 需要确认DIC所支持的mipi传输封包大小(180/96Byte的倍数)
- 需要确认flash的烧录地址
### 补偿

- offset经SPI从Flash搬运至Demura RAM，数据从RAM出来过**解压缩模块(decoder)** 后才计算。
- 通过2D Gain来进行效果调试，并输出.par文件
- 烧录定版2D Gain后需要回读确认2D Gain的值
### 注意事项

- 实际应用时IP处于常开状况，需要注意完整Reload时间与整机规格是否适配。
- 补偿时需要考虑extend Gamma和overflow，因为会存在补偿后>L255的情况。