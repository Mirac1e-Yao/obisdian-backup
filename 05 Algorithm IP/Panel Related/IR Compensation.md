# 基本概念
---
- IR Compensation，简称IRC。
- Local IR，简称LIR。
- Global IR，简称GIR。
- On Pixel Ratio，简称OPR。
## 为什么要做IRC？
---
- 用于解决面板ELVDD因走线IR以及Loading影响所致色亮度均一性较差的状况，会分为LIR和GIR。
- LIR 指的是因为所处位置不同导致的ELVDD drop程度不同，通用指标为面板远中近9点位置的亮度均一性。
- GIR 指的是因为画面Loading不同导致的ELVDD drop程度不同，通用指标为不同OPR亮度均一性/R+G+B=W/deltaE4；


# 补偿原理
---
- **GIRC核心点在于对画面Loading状况的判定**，通过对边界点（例如最大Loading对应DBVmax/GLmax/OPRmax，最小Loading对应DBVmin/GLmin/OPRmin）以及中间所取绑点光学数据的采集，以灰阶-亮度-Gamma公式完成对目标灰阶的计算，配置进DIC LUT。
- **LIRC核心点在于对画面区域的灰阶数据进行分块处理**，但于低灰阶下需要考虑精度，否则会出现栅格状的纹路异常，因为人眼在低亮条件下对1灰阶的差异还是比较敏感的。
- 对于非绑点部分的目标灰阶皆以线性内插算出。

## Chipone 架构举例
---
### RGBC(GIRC)
---
### 流程
---
- 依据采集到的R/G/B电流数据和不同OPR/灰阶/RGB/DBV的光学数据，在PC端C Model上计算得出绑点画面的电流I、等效电阻Relvdd/R、等效压差OPR(DeltaU)等参数后，进一步计算完善High/Low + DBV/Loading 4种情况的LUT，这些LUT所存储的数据就是补偿后需要输出的目标灰阶值。**整个过程可以理解为是基于一套理论模型推算出来的灰阶remap**，即在任一输入灰阶/OPR/DBV条件下，都能找到与之对应的输出灰阶。
![[Chipone GIR 架构.png]]
### 模型分析
---
- OLED器件的亮度与流过它的电流有关，基于Driver TFT的输出特性曲线可知，电流与VDS和VGS相关，其中VDS与ELVDD相关，VGS与Data电压相关。因此，为了抵消ELVDD因IR导致的电流差异，可以转换为通过调整Data电压实现。
- 简化模型中，将IR变化量提取为deltaU，data则提取为Udata。**Udata可理解为就是0~255的R/G/B灰阶数据，可以适用灰阶亮度转换公式以计算出补偿值，所以说是data域补偿。**
![[Chipone GIR 补偿原理1.png]]
![[Chipone GIR 补偿原理2.png]]
### GC(LIRC)
---
- 依据采集到的光学数据，在PC端C model上依据中心区域与其他划分区域的亮度灰阶关系，计算出每个区域对应的补偿Gain值。
![[Chipone LIR 架构.png]]
![[Chipone LIR 补偿原理.png]]


# 应用场景
---
- 补偿时需要考虑extend Gamma和overflow，因为会存在补偿后>L255的情况。