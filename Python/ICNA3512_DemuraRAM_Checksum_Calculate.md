# 调用到的函数
---
- strip()：
	- 该函数仅处理行首与行尾的字符，对于行中的数据不作处理，默认处理换行符与空格
# 源码
---
```python
# 初始化十六进制累加器（以整数形式存储）
X = 0
Y = 0
Z = 0 

# 初始化累加次数计数器
count_X = 0
count_Y = 0
count_Z = 0

# 定义累加函数，支持指定行范围
def accumulate_hex_values(file_path, start_line, end_line):
    global X, Y, Z, count_X, count_Y, count_Z
    with open(file_path, "r") as f:
        for idx, line in enumerate(f):
            # 只处理指定范围内的行
            if idx < start_line - 1 or idx >= end_line:
                continue
 
            hex_str = line.strip()  # 去除换行符和空格
            if not hex_str:
                continue  # 跳过空行
 
            try:
                value = int(hex_str, 16)  # 将十六进制字符串转为十进制整数
            except ValueError:
                print(f"第 {idx+1} 行数据无效: {hex_str}")
                continue
  
            # 按每3行的规律分组累加
            remainder = idx % 3
            if remainder == 0:
                X += value
                count_X += 1  # 累加次数加1
            elif remainder == 1:
                Y += value
                count_Y += 1  # 累加次数加1
            else:
                Z += value
                count_Z += 1  # 累加次数加1

# 调用函数，指定文件路径和行范围
file_path = "C:\\Users\\67064\\Desktop\\NG_DATA1.txt" #要把前4096Byte数据去掉

start_line = 1  # 起始行
# end_line = 1325056  # 结束行 需要减去256
end_line = 1324800  # 结束行 
accumulate_hex_values(file_path, start_line, end_line)

# 将结果转为十六进制字符串（大写，带0x前缀）
print(f"X = 0x{X:X}, 累加次数: {count_X}")
print(f"Y = 0x{Y:X}, 累加次数: {count_Y}")
print(f"Z = 0x{Z:X}, 累加次数: {count_Z}")
```
