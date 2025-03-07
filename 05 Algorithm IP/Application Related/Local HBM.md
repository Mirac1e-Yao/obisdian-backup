# 基本概念
---
- Local High Brightness Mode，简称LHBM。
- Finger Print Sensor，简称FPS。
- Pulse Width Modulation，简称PWM，亦有称Duty，指的是STE讯号的占空比。

# 补偿原理
---
- 因指纹功能衍生出的两个需求：一个是指纹区的效果控制，一个是维持非指纹区的亮度控制。
- 指纹区的效果控制包括FPS Pattern(需要预存进RAM)、**高亮高PWM(>97%)以满足光学CCD的采集效果** 和 形状/颜色/彩边/锯齿等高亮区域性状的调整；
- 非指纹区的亮度控制主要是处理在不同PWM条件下进入FPS Mode时所产生的亮度突变，具体会采取低PWM搭配AP蒙板 或 直接由DIC以Gain来压低Gamma/Data的形式来处理。
## 何为蒙板？
---
- 所谓蒙板可以理解为是针对非指纹区域的灰阶数据进行压低控制的行为。
- **在使用时需要确保进出FPS时，dbv切换需与蒙板在同一帧生效，否则可能出现视效异常。**

## Chipone 架构举例
---
![[Chipone FPS 功能架构.png]]

# 应用场景
---
- 整机光学指纹解锁