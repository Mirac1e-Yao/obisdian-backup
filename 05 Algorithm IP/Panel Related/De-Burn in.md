# 基本概念
---
- De-Burn in，简称DBI。
- Pixel Per inch，简称PPI，像素分辨率。
## 为什么要做De-Burn in？
---
- 当使用时间较长之后 或 像屏下摄像头区域的PPI与面板其他区域不同，因为OLED RGB子像素材料的衰减速度不同而引起色亮不均（烧屏）的状况，所以需要进行补偿使其恢复。
- 同时，应该还要有延长产品使用寿命的目的，因此补偿时是在保持均匀性的前提下降低欠老化子像素的亮度，而不是提高老化子像素的亮度，加速老化。
![[DBI 成因.png]]


# 补偿原理
---
- 核心在于记录面板显示时长（Data Counting，需要存储Flash）和 面板像素的衰退模型（Decay Curve），有了这两个模块的功能，才可以告知IC补偿模块中该如何对应补偿值。
![[DBI 补偿流程.png]]
## Chipone 架构举例
---
- 显示数据进来后会先以8x8 blocksize对数据进行取样，后基于衰退模型转换为Decay Factor，通过考虑DBV/Temp/FR等因素配置对应的Gain系数后与前一次Data Counting结果做累加，得出新的值后，存储进Memory（Flash）。到需要补偿的时候将Decay Factor提取出来并转换为对应的补偿值，完成补偿。

![[Chipone De-Burn in 架构.png]]


# 应用场景

- 与Demura/Over Drive同样都会占用RAM面积，因此存在依据项目重点优先级次序。
- 验证时主要提协助打通Flash流程，其余的衰退模型及调试配置皆有面板厂主导。
