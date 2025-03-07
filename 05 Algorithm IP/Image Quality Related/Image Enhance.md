# 基本概念
---
- Contrast Enhance，简称CE，对比度增强。
- Sharpness Enhance，简称SE，锐度增强。
- Sun Readable Enhance，简称SRE，阳光可视化增强。
- High Dynamic Range，简称HDR，高动态范围。
- HSL/HSB，H指Hue色相，S指Saturation饱和度，L/B指Lightness/Brightness明度。


# 补偿原理
---
- 此部分通用补偿IP应用较多的色彩学原理，但实际上在DIC使用的时候，主要就是对色彩模型进行切换来方便操作，通常为RGB转至HSL/HSB处理后再转回RGB，或者是直接对灰阶曲线进行拉伸。这些方法在DIC上补偿时bit位不够，可能导致画面细节更易受到损失。

# 应用场景
---
- 目前较多的方案是由AP处理后输出RGB数据给到DIC，因此DIC上的IP通常是关掉的。










