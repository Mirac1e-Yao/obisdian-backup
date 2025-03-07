# 采用.NET WinForms应用项目进行开发
---
![[C sharp APP开发.png]]

# 设计思路
---
- 目标：将ICNA3520 initialcode.txt 参照AN翻译寄存器设定，并以视窗形式显示出来；
- 拆解如下：
	- Load Function
		- [x] Load_Click
			- 通过Click事件，在导入OpenFileDialog控件后实现选中本地.txt并用streamreader将整个文本信息读取出来并存放到stringbuilder中。然后依据正则表达式的条件来将stringbuilder中的数据分类存入全局字典Dictionary AlltxtData中，键string用于存放依据正则表达式筛选出来的Group标识符，值stringList用来存储其后所跟的所有Register及Parameter；
			- 通过if判定行首是否存在“//”来过滤不需要的信息。
	- Transform Function
		- [x] ExtractByteArrayFromDictionary
			- 通过输入参数 GroupNumber、Register、ByteCount 和 ByteCountWithinSpace，将对应的文本数据从字典中提取出来并转换为byte数组，这个Byte数组存放的是对应Group-Register中的ByteCount数目的Parameter。
		- [x] Groupx_xxh_Transform
			- 存放对应Group-Register中Parameter含义的拆解逻辑，并输出至视窗；
			- SetIntToGroupBoxTextBox()：通过输入参数tabPageName、groupBoxName、textBoxName和data，用于将对应的data变量输出至windows form中对应tabPage-groupBox-textBox的视窗中。
		- [x] button_Transform_Click
			- Click事件触发程序执行ICNA3520_Intialcode_Transform()和Calculate()。
		- [x] ICNA3520_Intialcode_Transform
			- 存放了Groupx_xxh_Transform, ExtractByteArrayFromDictionary 这两个方法。
		- [x] Calculate
			- 主要是依据外部输入的OSC1/OSC2，通过读取当前状态的RTN/FR/DIV参数来输出对应Mode下的Hsync、PCLK和DCLK值，主要用于后续Information  Display Function 计算参考。
	- Information Display Function
		- [x] UpdateLabelValue_XXX
			- 将对应textBox与Label进行绑定，通过EventHandler实现Label值依据textBox值实时计算变化的逻辑，最后使用TextChanged更新Label值；
			- XXX表示不同的Register Parameter功能对应的输出逻辑。
	- Save Function
		- [x] ReadDataFromOtherTabPages
			- 将所有tabPage中所有的groupBox中所有的textBox值依照规定的顺序读回來（tabPage读取时按照默认顺序，groupBox读取时按照正序，textbox读取时则按照从左上到右下的顺序），存入的嵌套字典1中，键1为tabPage Name，值1为字典2，键2为groupBox Name，值2是textBox string list；
			- 通过excludedTabPageName来过滤掉tabPage_General的信息，防止存入字典。
		- [x] ExtractIntListFromGroupBoxTextBox
			- 从字典中提取对应groupBox 的 textBox List并返回string List；
		- [x] GroupX_XXh_SaveAsStringList
			- 将输入的int List转换为string List，将int List以某种规则拆解重组后形成string List，并在开头加入寄存器写值后返回string List，该string List中存放了寄存器及其Parameter的字符串信息。
		- [x] UpdateStringListToDictionary
			- 将重组后的string List依据键1 tabPage Name和键2 groupBox Name更新回字典2的值2中，作为最终输出至txt文本的集合。
		- [x] SaveStringArrayToFile
			- 使用SaveFileDialog控件搭配streamwriter选择存入的txt本地路径，之后遍历嵌套字典输出。
	

## OpenFileDialog 和 StreamReader 有什么区别？
---
- OpenFileDialog 是.NET中所提供的一个类，能够让用户浏览文件系统并选择一个或多个文件，但不与选择的文件内容进行交互；
- StearmReader 也是.NET中所提供的一个类，负责实际打开文件、操作(读写)文件；
- **这两个类会一起搭配使用来完成从选择到操作具体文件的过程。**

## Regex、Match 和 MatchCollection 有什么区别？
---
- Regex 是.NET中所提供的一个类，用于处理正则表达式；
- Match 也是.NET中所提供的一个类，用于表示单个匹配项；
- MatchCollection 是.NET中所提供的一个类，通常是作为 Regex.Match或Regex.Matches的返回类型，是匹配项的一个集合；
- **这三个类在想要获取并操作满足正则表达式的匹配项集合时，会同步使用**

## Dictionary 、List 和 Array 有什么区别？
---
- Dictionary 是.NET中所提供的一个类，属于一种数据结构，提供键和值的组合方式用于快速检索其中的数据；
- List与Array最本质的区别在于，Array是分配一块固定空间(栈)，而List的空间大小(堆)是动态变化的，使用上灵活许多；

## String[]、String List 和 String Builder 有什么区别？
---
- string 是不可变的字符串类型，属于数据的载体，比较适合不会频繁修改变量数据的用途；
- stirng List 是泛集合类型，可以存储多个字符串对象，可以动态添加和删除列表中的对象；
- string builder是可变的字符串类型，用于大批量操作字符串时减少资源开销；
- **string可以理解为定量，一旦设定不希望频繁的计算变更；string List则理解为大小数量可变的字符串集合；string builder可以理解为是string List的低功耗版本，在资源利用上较节约。**

## LINQ查询法
---
- LINQ（Language Integrated Query）是一种语言集成查询技术，它允许在 C#（以及其他.NET语言）中以一种统一的方式进行数据查询和操作，无论是针对内存集合、数据库、XML文档或其他数据源，常见操作如下：
	- 选择Select：var result = from item in collection select item.Property;
	- 过滤Where：var result = from item in collection where item.Property > 10 select item;
	- 排序OrderBy：var result = from item in collection orderby item.Property ascending // 或者 descending select item;
	- 分组GroupBy：var result = from item in collection group item by item.Property into grouped select grouped;
	- 连接Join：var result = from item1 in collection1 join item2 in collection2 on item1.Property equals item2.Property select new { item1.Property, item2.Property };

## EventHandler
---
- EventHandler是一个事件委托类型，它可以针对某个控件对象创建事件，使其满足条件时动作；
- 可以搭配 lambda表达式来进行使用（创建匿名的方法函数）EventHandler handler = (sender, e) => { ... };