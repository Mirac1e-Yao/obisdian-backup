# 基本概念
---
- 会以固定的倍率对输入图像进行放大处理。
![[Scaling介绍.png]]

# 补偿原理
---
- 采用双线性插值的方式进行处理
## Chipone 架构举例
---
- Scaling up x2.0
![[Chipone upscaling x2.0原理.png]]
- Scaling up x1.5
![[Chipone upscaling x1.5原理.png]]
- Scaling up x1.33
![[Chipone upscaling x1.33原理.png]]

# 应用场景
---
- 对于2K分辨率的应用，可能会让AP通过送小图到DIC再复原的方式来节省功耗；
- 例如x1.33x：1080p(FHD)→1440(WQHD）