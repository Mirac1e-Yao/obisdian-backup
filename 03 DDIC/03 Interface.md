# MIPI
## PHY Layer
---
## Lane Management
---
## Protocol
---
## Application
---

# DSC
---
## 算法原理
---
- 算法原理是将未进行压缩的原始图像中的像素点以光栅扫描顺序 实时输入进编码器中，编码器对进入的像素进行压缩形成特定的比特流格式，然后将部分比特流存储在编码器内的速率缓冲器中。随后，DSC比特流通过标准范围之外的传输层，如MIPI DSI、HDMI等接口，进行从编码器端到解码器端的实时传输。最后，解码器端接收到比特流之后，先暂时将其存储在解码器内的速率缓冲器中，形成解压像素，随后以光栅扫描的方式实时传输，最后输出进行显示。
- RGB => YCoCg => Bistream => YCoCg => RGB
## 设计参数
---
- rc_model_size：
	- 决定了速率控制模块的大小，该参数会影响压缩效率和质量损失；
	- Typical值取8KB(即8192)，便于兼容其他设备；
- initial_offset：
	- 用于设定比特流的初始填充位置，该参数的选择影响比特流在开始时的填充情况，从而影响压缩器的初始状态；
	- typical值为rc_model_size的一半，4KB(即4096)；
- min_buffer_size： 
	- VESA DSC 1.1标准中对存储码流单元模块的大小有规定最小值；
	- 具体计算可参考 min_buffer_size = rc_mode_size - initial_offset + ceil(initial_xmit_delay x bits_per_pixel) + groupPerLine x first_line_bpg_offset；
	- 其中ceil(initial_xmit_delay x bits_per_pixel)向上取整结果通常为rc_mode_size/2；
	- groupsPerLine，因1group=3pixels，当以1slice/line为指标时，该值为ceil(最大分辨率/3)；
	- V1.2a最大支持4 muxwords(Y1/Y2/Co/Cg)，V1.1仅支持3 muxwords(Y/Co/Cg)；

## slice、chunk、group、muxword的联系
---
- 一副图像(Picture)会被切分成若干个Slice，每个Slice依照RGB(转成YCoCg)/YCbCr分成3组Substream，每3个连续Pixel Data构成1个Slice Group送入Encoder进行编码，**因此DSC Encoder/Decoder工作频率为1T3P，Slice Group为DSC的基本处理单位**；编码完成后经Multiplex合并成Bistream经Display Link(MIPI/DP/HDMI...)传输到Decoder，经Demultiplex、Decoder还原为Picture。
![[DSC-1.2a Encoding Process.png]]
![[DSC-1.2a decoding Process.png]]
- 针对encoder的算法预测模式，有MMAP/BP 2种模式，**其中BP因为csot较大，属于可选项，使用block_pred_enable控制**；
- 针对substream在multiplex进行交织(interlace)时有规定**最小单位为muxword，大小为48bit@8/10bpc，64bit@12/14bpc**；
- chunk是slice中的传输单位，不同slice中的同一chunk送完后再送下一chunk，概念类似Packet；

## 应用
---
- 

# SPI


# I2C


# eDP
