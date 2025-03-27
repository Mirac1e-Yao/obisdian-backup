# Flash烧录不良分析
---
## 需要资料

- Flash Erase/Program Flow Code
- **TEST.BIN烧录档：提供golden data与checksum用于与NG样品对比**
- MIPI → Demura RAM CRC Calc. 存放计算值的寄存器 *Group 1 EDh@ICNA3512*
- SPI → Demura RAM Checksum Calc. 存放计算值的寄存器 *Group D E7h@ICNA3512*
- Demura RAM 数据读取 Flow Code
## 解题方法

1. CRC Calc.：用于判断经MIPI写入Demura RAM的过程有无异常，golden值需要从OK样品读取
2. Checksum Calc.：通过寄存器1st  Byte来判断Demura RAM与Flash之间的传输有无异常，2nd ~7th Byte为Flash Reload回Demura RAM时的checksum计算值；8th ~13th Byte为Flash中被烧录的checksum值(非计算值，正常情况下需要与BIN档golden checksum比对一致) ，需注意凭此方法不能判断NG发生在Demura RAM还是Flash
3. 读取Demura RAM中的数据进行checksum计算比对：依据规则计算出理论checksum值，与Flash Reload时DDIC Checksum Calc.模块的计算值比较，判断Calc.模块有无计算出错，需找AE支援
4. 逻辑分析仪抓取CS/SCK/SDI/SDO：获取Demura RAM向Flash传输的data值，用于与BIN档的golden data比对，来判断NG发生在Demura RAM还是Flash
5. 调整SPI SCK_FREQ：等效于延长烧录时间，用于排除由于时间不够导致NG
6. 调整VDDI / 调整SPI采样延时：若调整有效，倾向于Flash异常，可抓取#OK和#NG的SCK与SDI/SDO相位延迟进行比对，将波形结果输出给Flash厂商判断 *@来源参考(20241213)ICNA3508A_Visionox_O16_客退异显不良分析_O1.pptx*
7. Flash ID & Status Register Read：用于判断并确认与Flash的通讯连接状况
8. Flash / FPC Swap：用于快速定位问题点是否与Flash相关

![[解题分析脉络.png]]

## SOP

![[Pasted image 20250327175734.png]]