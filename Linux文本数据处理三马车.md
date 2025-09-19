# 正则表达式——辅助文本处理的利器
正则表达式：是对字符串操作的一种逻辑公式，就是用事先定义好的一些特定字符、及这些特定字符的组合，组成一个“规则字符串”，这个“规则字符串”用来表达对字符串的一种过滤逻辑。**在grep的应用里，正则表达式只是在pattern里的结构**
```
^ 行首   # grep '^T' 查看T开头的行
$ 行尾   # grep 'T$' 查看T结尾的行
. 换行符之外的任意单个字符 # grep 'TTA.$' 查看TTAx结尾的行，模糊关键词
? 匹配之前项0次或者一次    (用\？或者 -E开启)前面那个字符0次或1次的情况，故TTGAAA\?可查TTGAA、TTGAAA.            + * 同理（但是*是基础正则，不用+E）
+ 匹配1次或者多次
* 匹配0次或者多次
{n} 匹配n次
{n,} 匹配至少n次
{m,n} 至少m,最多n
[] 匹配任意一个   grep [BC] 有B\C中任意一个都可。还能AT[BC] ATB\ATC
[^] 排除字符
| 或者
```
# 文本处理三架马车
## grep
一种强大的**文本搜索工具**，它能使用正则表达式匹配模式搜索文本，并把**匹配的行**打印出来——对于某文件中隐约记得的信息，可通过此工具搜索到
```
grep [options] pattern file #pattern ：需要查找的文本，需''
-w：word 精确查找某个关键词 pattern（可以过滤掉下划线的词）
-c：统计匹配成功的行的数量 （注意一行可能多个关键词被匹配）
-v：反向选择，即输出没有匹配的行 （常用！！用来过滤特定行） 
-n：显示匹配成功的行所在的行号 （常用）
-r：从目录中查找pattern（不知道在哪个具体文件，但确知在某个目录下，会去寻找该目录下的非压缩文件）
-e：指定多个匹配模式
-f：从指定文件中读取要匹配的 pattern（常用！用vim写一个新的文件，每个关键词放一行。最终结果是含各个关键词的行的并集）
-i：忽略大小写
 
 ## 常用形式
 cat 文件名 | grep 文本
 less -SN 文件名 | grep -w -e 文本1 -e 文本2 -e 文本3
 less -SN 文件名 | grep -w -f 关键词文件 
```
 
## Sed
sed：流编辑器，一般用来对文本进行**增删改查**
>流：在命令行中运行一个程序时，这个程序产生的正常输出就是通过**标准输出流**发送的，最终显示在你的终端屏幕上。而sed作为流编辑器，在处理文件时，不是一次性将整个文件加载到内存中进行编辑，而是**按行读取文件内容，对每一行应用指定的编辑命令，然后立即输出结果**。
```
sed [-options] 'script' file(s)  
## options
-n ：禁止显示所有输入内容，只显示经过sed处理的行(常用)
-e ：直接在命令模式上进行 sed 的动作编辑，接要执行的一个或者多个命令(常用)
-f ：执行含有 sed 动作的文件
-r ：sed 的动作支持的扩展正则(默认基础正则)
-i ：直接修改读取的文件内容，不输出。

## script:[address][!] command
# 常用address
2 ∶第 2 行
2,4 ∶第 2 行到第 4 行
2,$ ∶第 2 行到最后一行，$ 表示最后
2~3 ∶从第 2 行开始，每隔 3 行取一行2、5、8
2,+4 ∶从第 2 行到 2+4 行
/pattern/ ：匹配上 pattern 的行 （这个也很常用）
[!] ：表示否定，取反： '2 !' 表示除了第二行

# command :增删改查——2增1删3改1查
a∶append，在指定行的后增加一行，内容为 a 的后面接的字串
i∶insert，在指定行的前增加一行，内容为 i 的后面接的字串
d∶delete，删除某一行或者某几行，也可以指定删除匹配上的行（常用来删标题行）
c∶change，改变指定行的内容
s∶更改或替换字符串，使用格式为 's/pattern/new/[flags]'，把pattern替换成new，默认只替换一个，可以指定flags=g\1\2（操作检索到的第几次）  （常用） s前面不写地址的话，则是默认遍历整个文件
y∶转换，实现字符一对一转换，格式 ‘y/abc/ABC/'
p∶print，把匹配或修改过的行打印出来，通常与–n参数合用 sed -n '/待查找内容/p'
sed p的优势在于，可以一边替换一边打印：sed -n 's/原文本/修改后文本/ p' 
sed p之后可以再接 less 以查看大文件

##在sed时往往需要考虑一个先后顺序，往往是先增加后删改，因为如果删除的目标行是增加行的地址，那么增加就无法执行了
```
## awk
awk：也称 gawk，编程语言，可对**文本和数据**进行处理
awk 在读取一行文本时，会用**预定义的字段分隔符**划分每个数据字段，并分配给一个变量$0（整行）、1、2...、NF（最后一个）。默认的字段分隔符是**任意空白字符**（如：空格 or 制表符），也可以用 -F 参数自定义分隔符。
常见参数：
F，fields，设置字段分隔符；
-v，var=value 定义awk程序中的一个变量及其默认值
```
用法：awk [options] '{script}' file
基础结构： '{script}' 
匹配结构： '/pattern/{script}'
扩展结构： 'BEGIN{script} {script} END{script}'
```
```
#awk 内置变量
FS ：定义输入字段分隔符，Field Separator，同 –F
RS ：定义输入记录分隔符，Record Separator
OFS ：定义输出字段分隔符，Out Field Separator
ORS ：定义输出记录分隔符，Out Record Separator
NF ：数据文件中的字段总数，可以简单理解为列数
NR ：已处理的输入记录数，可以简单理解为行数
```

### awk操作例子
```
# 基本用法
less -S  Data/example.gtf 
less -S  Data/example.gtf | cut -f 9 | less -S
less -S  Data/example.gtf | awk '{print $9}' | less -S
less -S  Data/example.gtf | awk '{print $9,$10}' | less -S
less -S  Data/example.gtf | awk -F '\t' '{print $9}' | less -S
# 扩展结构（即，允许操作三次）
less -S Data/example.gtf | awk '/UTR/{print $0}' | less -S
less -S Data/example.gtf | awk 'BEGIN{print "find UTR feature"} /UTR/{print $0} END{print "end"}'
less -S Data/example.gtf | awk 'BEGIN{FS="\t"} {print $9}' | less -S
less -S Data/example.gtf | awk 'BEGIN{FS="\t"} {print NR,$9}' | less -S
# 条件和循环 （允许用条件语言和循环，对特定行进行操作）
awk ' { if (判断条件) {yes} else {no} } '
awk ' { for (循环条件) {循环语句} } '
less -S  Data/example.gtf | awk '{if($3=="gene") {print $0} }' | less -S
less -S  Data/example.gtf | awk '{if($3=="gene") {print $1,$2,$3} }' | less -S
less -S  Data/example.gtf | awk '{for(i=1;i&lt;4;i++){print $i} }' | less -S
less -S  Data/example.gtf | awk '{for(i=1;i&lt;4;i++){print $i} }' | paste - - - | less -S
# 运算和字符处理 （可以输出变量的数学计算结果）
+ （加），- （减）， * （乘），^ （幂）
/ （除），** （平方）， % （取余）
int(x) x的整数部分，取靠近零一侧的值
log(x) x的自然对数
less -S  Data/example.gtf | awk '/exon/{print $5-$4}' | less -S
less -S  Data/example.gtf | awk '{print $5/$4}' | less -S
less -S  Data/example.gtf | awk '{print int($5/$4) }' | less -S
less -S  Data/example.gtf | awk '{print int($5/$4 + 0.5) }' | less -S
less -S Data/example.gtf | awk 'BEGIN{FS="\t";OFS="\t"} {gsub("gene","Gene",$3);print $0}' | less -S
```

#### 小技巧
`alias le='less -SN'  # 通过alias可自定义命令的简称，此后输入简称也可达到同样的效果`
所有的alias可以在`vim ~/.bashrc `里/alias找到
**输入文件不能作为输出文件** ：在Linux里，如果 command file1 ...>file1， 即希望将文件原位保存的话，反倒会导致file1清空，存储变为0。这种存储是一步完成不了的，需要个中间文件
**括号** ：命令行内容的操作一般是从左往右执行的，但是如果用$()，括号内的命令将会优先计算，其结果将被传递至括号外。 
	
## Linux常见符号及其意义
![[Pasted image 20250919173822.png]]
