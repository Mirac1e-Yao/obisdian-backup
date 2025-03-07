# 基本概念
---
- Average Pixel Level，简称APL，平均像素灰阶。
![[ELVDD Comp. 走线示意.png]]
## 为什么要做ELVDD补偿？
---
- ELVDD因走线IR或画面切换（Black->White）时产生drop状况，若不进行补偿则会出现在不同的平台上亮度效果表现不一致 或  画面切换时出现暂态画异。 


# 补偿原理
---
- 通过ELVDD_Det侦测面板所提供的ELVDD测点，IC内部则设置好对应的trim表，如此便可将ELVDD电压的drop情况与Gamma Vgsp亮/Vgmp暗态匹配，实现通过控制亮/暗态电压来代偿ELVDD电压drop所引起的亮度差异。
## Focal 架构举例
---

![[FocalTech ELVDD Comp. 架构.png]]
## 为什么面板测点位置会影响APL 1%的亮度？
---
- 首先，**面外接法的亮度大于面内接法**。
- 分析如下：ELVDD input到AVC测点的阻抗为R1，AVC测点到面板中心区域的阻抗为R2，R1部分的阻抗所造成的压降会被DIC侦测到而补偿，且I1与I2主要受面板发光材料的影响（*HBM Loading大于APL 1%，I1>I2*），**因此R2与APL 1%中心点的亮度呈正相关**。
![[ELVDD面内与面外接法的差异.jpg]]


# 应用场景
---
- 设计原理图时需要为DIC ELVDD_AVC Pin选择Panel面内或面外的测点，测点的位置会影响到最终的实际补偿效果，主要应用为峰值亮度/APL 1%；
- **需要考虑补偿生效的时间，否则可能会引起闪屏异常；**
- **需要考虑检测补偿的精度，否则可能会引起亮度数据的周期性突变；**
