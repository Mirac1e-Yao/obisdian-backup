# 基本概念
---
- Timing Controller：时序控制器

## 为什么interface的sync信号与TCON输出不同步

- DDIC在某些图像处理场景时会使用到linebuffer，所以造成了输出上存在delay

## DDIC是如何完成对信号的采集

1. ​**输入缓冲（Input Buffer）​**  
    外部信号通过引脚进入芯片后，首先经过输入缓冲器，消除信号振铃或噪声，并转换为芯片内部电压电平。
2. ​**滤波与整形（Filtering & Conditioning）​**  
    使用RC滤波电路或施密特触发器（Schmitt Trigger）消除高频噪声，并将信号边沿锐化（例如将缓慢上升沿变为陡峭边沿）。
3. ​**边沿检测（Edge Detection）​**  
    通过数字逻辑（如D触发器）检测信号的上升沿或下降沿，触发内部状态机或计数器。
4. ​**时序对齐（Timing Alignment）​**  
    将检测到的边沿与内部时钟（Pixel Clock）同步，确保后续逻辑在正确时间处理数据。