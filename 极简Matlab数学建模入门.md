# Matlab数学建模入门

最简单的快速学习方法：直接去Matlab官网查询函数用法以及示例！

本文档中部分代码是伪代码，仅供演示用法。

## 实用技巧

分割代码：.m文件可以使用%%＋回车分割代码，然后可以分段运行，类似jupyter。

调试代码：左侧行号单击设置断点，如果想要运行单行代码，可以鼠标右键选择“在命令行窗口中执行所选内容”。

实时输出：建议使用实时脚本，即.mlx文件，会在编辑器右侧实时输出绘制的图片。matlab默认输出每行代码的运算结果，如果不希望输出的变量太多，可在每行代码后添加分号(;)取消输出。如果希望按照一定格式输出，可以使用fprintf()或者disp()函数。

查找具体数据：工作区查找变量具体内容，双击可进入类似于excel表格的页面。

help方法：对于不熟悉的函数可在命令行窗口使用help方法查询，返回参数用法以及示例。

```matlab
>> help ode45
 ode45 - 求解非刚性微分方程 - 中阶方法
    此 MATLAB 函数（其中 tspan = [t0 tf]）求微分方程组 y'=f(t,y) 从 t0 到 tf 的积
    分，初始条件为 y0。解数组 y 中的每一行都与列向量 t 中返回的值相对应。
    ......
```

并行计算：在使用优化求解器求解时，有时候因为目标函数非常复杂，求解非常慢，这时候可以使用并行运算加快计算速度，方法如下：

```matlab
CoreNum=6; %设定机器CPU核心数量
if isempty(gcp('nocreate')) %如果并行未开启
    parpool(CoreNum);
end
parfor ...
    for ...
    ...
    end
end
```

corenum是matlab最大可以同时使用的cpu内核数，不同电脑会不同，在开启并行运算时，最外层for循环应改为parfor。

## 矩阵和数组

#### 一维数组

```matlab
a=[1 2 3 4]
a=[1,2,3,4] %行向量
b=[1;2;2] %列向量
```

#### 二维数组(矩阵)

```matlab
a=[1 5 6; 2 5 7; 5 1 2]
```

**需要注意，matlab的矩阵在内存中是按列存储的。**

基本操作：

##### 初始化

不初始化，在计算过程中程序自动分配大小，数据量较小时运行速度慢，数据量大时会报错。

```matlab
>> z=zeros(5,2)

z =

     0     0
     0     0
     0     0
     
>> z=ones(2,4)

z =

     1     1     1     1
     1     1     1     1
     
>> z=pi*ones(2)

z =

    3.1416    3.1416
    3.1416    3.1416

>> z=rand(2,3) %生成均匀分布的随机数

z =

    0.8147    0.1270    0.6324
    0.9058    0.9134    0.0975

>> randi([2,15],3,2) %生成一定范围内随机整数

ans =

     5    15
     9     4
    15    15
```

其他随机数生成函数可自行查询。

##### 索引

尤其是直接提取行/列的操作。

```matlab
>> a=[2,3,4;1,2,5]

a =

     2     3     4
     1     2     5

>> a(2)

ans =

     1
     
>> a(1,3)

ans =

     4
     
>> a(1,:)

ans =

     2     3     4
     
>> a(:,2)

ans =

     3
     2

>> a(:,end)
%end表示矩阵最后一列或最后一行，在矩阵较长时便于索引，可使用end-1索引倒数第二行或者列，以此类推
ans =

     4
     5

```

##### 逻辑索引

```matlab
>> y=[1 7 3 9 2 8 9 10]

y =

     1     7     3     9     2     8     9    10

>> find(y>3) %逻辑索引查找，返回索引

ans =

     2     4     6     7     8

>> y=y(y>3) %利用逻辑索引筛选数值

y =

     7     9     8     9    10

```

##### 矩阵拼接

```matlab
>> b=[2,3,4]

b =

     2     3     4
     
>> c=[1,2;3,5]

c =

     1     2
     3     5

>> [a;b]

ans =

     2     3     4
     1     2     5
     2     3     4
     
>> [a,c]

ans =

     2     3     4     1     2
     1     2     5     3     5
```

##### 矩阵计算

加减法，数乘运算

```matlab
>> a+2

ans =

     4     5     6
     3     4     7

>> 1.2*a

ans =

    2.4000    3.6000    4.8000
    1.2000    2.4000    6.0000
    
>> a' %转置运算

ans =

     2     1
     3     2
     4     5

```

##### 矩阵乘法

注意点乘( .* )与矩阵乘法( * )的区别

```matlab
a =

     1     4     5
     1     2     1

>> b=[3,2,1;1,8,9]

b =

     3     2     1
     1     8     9

>> a.*b

ans =

     3     8     5
     1    16     9

>> a*b'

ans =

    16    78
     8    26

>> a'*b

ans =

     4    10    10
    14    24    22
    16    18    14
```

##### 除法(求逆)运算

a/b=a* div(b)=a*b^-1

**需注意：a/b相当于求解关于x的线性方程组xA=B，a\b相当于关于x的线性方程组Ax=B。**

```matlab
>> b=[2,3,1;1,5,7;3,2,5]

b =

     2     3     1
     1     5     7
     3     2     5

>> inv(b)

ans =

    0.1930   -0.2281    0.2807
    0.2807    0.1228   -0.2281
   -0.2281    0.0877    0.1228

>> b^-1

ans =

    0.1930   -0.2281    0.2807
    0.2807    0.1228   -0.2281
   -0.2281    0.0877    0.1228

>> a=[2,1,3;3,5,6;1,2,1]

a =

     2     1     3
     3     5     6
     1     2     1

>> a/b

ans =

   -0.0175   -0.0702    0.7018
    0.6140    0.4561    0.4386
    0.5263    0.1053   -0.0526

>> a*inv(b)

ans =

   -0.0175   -0.0702    0.7018
    0.6140    0.4561    0.4386
    0.5263    0.1053   -0.0526

>> a*b^-1

ans =

   -0.0175   -0.0702    0.7018
    0.6140    0.4561    0.4386
    0.5263    0.1053   -0.0526

>> a./b

ans =

    1.0000    0.3333    3.0000
    3.0000    1.0000    0.8571
    0.3333    1.0000    0.2000

```

需注意点除与除的区别。

##### 幂运算

```matlab
>> a^2

ans =

    10    13    15
    27    40    45
     9    13    16

>> a.^2

ans =

     4     1     9
     9    25    36
     1     4     1

```

##### 对角矩阵

使用diag函数将向量转化为对角矩阵。

```matlab
>> v=[1,-4,2,-3]

v =

     1    -4     2    -3

>> a=diag(v)

a =

     1     0     0     0
     0    -4     0     0
     0     0     2     0
     0     0     0    -3

```

##### 线代常用计算

```matlab
>> det(a) %求行列式值

ans =

   -8.0000

>> rref(a) %求行最简形矩阵

ans =

     1     0     0
     0     1     0
     0     0     1

>> rank(a) %求秩

ans =

     3

>> c=[2,3,4;1,1,1]

c =

     2     3     4
     1     1     1

>> null(c,'r') %求基础解系

ans =

     1
    -2
     1

>> [x,y]=eig(a) %求特征向量和特征值

x =

   -0.8847    0.3015   -0.5657
    0.4423    0.9045   -0.4243
    0.1474    0.3015    0.7071


y =

    1.0000         0         0
         0    8.0000         0
         0         0   -1.0000

>> sum(a,2) %按列求和，也可按行求和

ans =

     6
    14
     4

>> unique(a,'stable') %提取唯一值

ans =

     2
     3
     1
     5
     6

dot(A,B)%点乘
cross(A,B)%叉乘
```

在数据处理过程中，有时需要提取数组中的唯一值，此时采用unique函数非常方便。

##### 重构矩阵（reshape函数）

reshape函数重构矩阵时按列重新排布，要求重构后的大小等于原先矩阵的大小，否则会报错。

```matlab
>> a=[4,2,2;1,3,5;3,4,6;1,5,5]

a =

     4     2     2
     1     3     5
     3     4     6
     1     5     5

>> reshape(a,[2 6])

ans =

     4     3     2     4     2     6
     1     1     3     5     5     5

>> reshape(a,[],2)

ans =

     4     4
     1     5
     3     2
     1     5
     2     6
     3     5

```

##### 复制矩阵(repmat)

常用于按列复制矩阵或者拼接不同大小的矩阵

```matlab
A = (1:3)';  
B = repmat(A,1,4)

B = 3×4

     1     1     1     1
     2     2     2     2
     3     3     3     3

A = [1; 3; 5];
B = [2; 4];
T = [repelem(A,numel(B)) repmat(B,numel(A),1)]

T = 6×2

     1     2
     1     4
     3     2
     3     4
     5     2
     5     4

B = repmat(A,1,2)

B =

   100     0     0   100     0     0
     0   200     0     0   200     0
     0     0   300     0     0   300
```

##### 创造等间距向量

```matlab
>> d=0.1:0.1:2

d =

  列 1 至 14

    0.1000    0.2000    0.3000    0.4000    0.5000    0.6000    0.7000    0.8000    0.9000    1.0000    1.1000    1.2000    1.3000    1.4000

  列 15 至 20

    1.5000    1.6000    1.7000    1.8000    1.9000    2.0000

x = linspace(0,1,25)

x =

  列 1 至 14

         0    0.0417    0.0833    0.1250    0.1667    0.2083    0.2500    0.2917    0.3333    0.3750    0.4167    0.4583    0.5000    0.5417

  列 15 至 25

    0.5833    0.6250    0.6667    0.7083    0.7500    0.7917    0.8333    0.8750    0.9167    0.9583    1.0000
```

#### 多维数组

matlab支持多维数组，以三维数组为例，可采用如下赋值方法：

```matlab
A(:,:,1) = [2 4; -2 1];
A(:,:,2) = [1 2; -5 3];
A(:,:,3) = [4 4; 1 -3];
```

## 语言基础知识

#### 运算符

算术运算符以及逻辑运算符与c基本相同，需注意，matlab中不等于为"~="而不是"!="，"~"表示非运算。

**一定注意区分数值的乘除幂运算和矩阵的乘除幂运算。**

[运算符优先级](https://ww2.mathworks.cn/help/matlab/matlab_prog/operator-precedence.html)

#### 数值类型

以存储大小：

| 数值类型                   | 特征                   |
| -------------------------- | ---------------------- |
| double                     | 双精度浮点型           |
| single                     | 单精度浮点型           |
| int8/int16/int32/int64     | 8/16/32/64位有符号整数 |
| uint8/uint16/uint32/uint64 | 8/16/32/64位无符号整数 |
| logical                    | 逻辑值(0 False/1 True) |
| char                       | 字符型                 |

同c，string作为字符数组存在。

以数据特征：

| 数值类型 | 特征         |
| -------- | ------------ |
| complex  | 复数         |
| NaN      | 非数值或者空 |
| Inf      | 无穷         |

对于未知数据类型，未知大小的变量，可以使用whos方法查询相关信息

```matlab
>> c=3+4i

c =

   3.0000 + 4.0000i

>> whos c
  Name      Size            Bytes  Class     Attributes

  c         1x1                16  double    complex   

```

可以看到complex复数类型本质上也是继承自double类型数据。

可以用complex函数创建complex类型矩阵。

```matlab
x = rand(3) * 5;
y = rand(3) * -8;

z = complex(x, y)
z =
   4.7842 -1.0921i   0.8648 -1.5931i   1.2616 -2.2753i
   2.6130 -0.0941i   4.8987 -2.3898i   4.3787 -3.7538i
   4.4007 -7.1512i   1.3572 -5.2915i   3.6865 -0.5182i
```

可以用real和imag函数提取复数的实部和虚部。

```matlab
zr = real(z)
zr =
    4.7842    0.8648    1.2616
    2.6130    4.8987    4.3787
    4.4007    1.3572    3.6865

zi = imag(z)
zi =
   -1.0921   -1.5931   -2.2753
   -0.0941   -2.3898   -3.7538
   -7.1512   -5.2915   -0.5182
```

可以使用isreal函数判断一个数或者数组是否是实的，返回逻辑值0或1。

```matlab
>> isreal(c)

ans =

  logical

   0

```

同理可以使用isnan，isinf函数判断一个数是否为非数值。

需要注意isreal只能返回一个矩阵中是否有复数，并不能得到复数的具体位置。

对于去除一个数组中的所有复数值，可以采取逻辑索引的办法：

```matlab
>> c=[3,4+i,5,6,8,4,2+i,5,8-i]

c =

  列 1 至 7

   3.0000 + 0.0000i   4.0000 + 1.0000i   5.0000 + 0.0000i   6.0000 + 0.0000i   8.0000 + 0.0000i   4.0000 + 0.0000i   2.0000 + 1.0000i

  列 8 至 9

   5.0000 + 0.0000i   8.0000 - 1.0000i

>> c(find(imag(c)))=NaN

c =

     3   NaN     5     6     8     4   NaN     5   NaN

>> c=c(~isnan(c))

c =

     3     5     6     8     4     5

```

对于矩阵中存在复数，NaN以及Inf的情况，一般采取按行去除，做法与上述过程类似，也可以采用数值替换的方式进行预处理，复数可采用实部填充或者模长填充的方式，具体方法请读者自行研究。

#### 全局变量声明

与c类似，matlab可自行定义全局变量，关键字为global。

```matlab
global theta
theta=pi/3;
```

#### 其他数据类型

除了矩阵以外，matlab还提供诸如字符串数组，时间数据类型，分类数组，表，元胞数组、字典和结构体。这里简单介绍后四者的应用。

##### 表(table)

一般使用readtable函数从.xls、.xlsx以及.csv文件中读取数据，在工作区中生成对应的表，需对表进行数据清洗之后转化成矩阵，方便进一步运算。由于笔者并不擅长使用matlab进行数据清洗，在这里不做赘述，matlab提供了数据清洗器(Data Cleaner)工具箱，具体用法请参照官方文档。

除了readtable函数，load关键字可以加载数据集，也可以导入.txt文件中的变量到关键字。

```matlab
data=table2array(readtable('data.xlsx'))
```

除了table2array方法，matlab还支持array2table，table2cell等数据类型转换函数。

matlab还提供了writetable函数可供批量将数据写入表格。

##### 元胞数组(cell)

元胞数组可以是任意长度，任意内容的数组，一般用于长度不确定的数组的存储。

例如：

```matlab
>> c={[1 2 4 6],[2 3],'matlab'}

c =

  1×3 cell 数组

    {[1 2 4 6]}    {[2 3]}    {'matlab'}

```

可以按列提取或者添加：

```matlab
>> c(:,1)

ans =

  1×1 cell 数组

    {[1 2 4 6]}

>> c(:,4)={[2 4 1]}

c =

  1×4 cell 数组

    {[1 2 4 6]}    {[2 3]}    {'matlab'}    {[2 4 1]}

```

##### 字典(dictionary)

类似于python，matlab也提供字典类型，包括key值和value值。

python：

```python
stu={'张三':18,'李四':19,'小明':20}
```

matlab:

```matlab
>> key=["张三","李四","小明"]

key = 

  1×3 string 数组

    "张三"    "李四"    "小明"

>> value=[18,19,20]

value =

    18    19    20

>> stu=dictionary(key,value)
```

**在r2022b版本后才能使用dictionary函数，在此之前应使用containers.Map。**

**字典在python中用处很大，在数据清洗阶段用于替换字符串类型数据。**

##### 结构体(struct)

不同于c，结构体在matlab中本质上是一个对象，大量统计相关的函数在调用时会返回一个结构体，需要读者了解相应函数输出对象的含义才能加以使用。这里只介绍如何创建一个结构体以及如何提取结构体中的内容。

创建：

```matlab
>> d.a1="func"
d.a2=[3 4 5 6]

d = 

  包含以下字段的 struct:

    a1: "func"
    a2: [3 4 5 6]

```

提取：

```matlab
>> s=d.a2

s =

     3     4     5     6
%此时s的数据类型是向量
```

#### 循环及条件语句

if语句需遵循以下格式

```matlab
if expression
    statements
elseif expression
    statements
else
    statements
end
```

matlab的for循环对向量进行运作，需要熟悉向量索引才能进行编程。

下面给出matlab与c，python中for循环的类比，以阶乘为例：

c

```c
int n=8,sum=1;
for(i=1,i<=n,i++)
{
    sum=sum*i;
}
```

python

```python
sum=1,n=8
for i in range(n+1):
    sum=sum*i
```

matlab

```matlab
sum=1,n=8;
for i=1:1:n
    sum=sum*i;
end
```

循环的序列可以是任意序列如：

```matlab
for i=0.1:0.5:10
```

matlab也支持遍历数组

```
for i=[3 2 5 6 8]
```

类比python

```python
list=[3,2,5,6,8]
for i in list
```

break，continue，while的用法类似于c，不做说明。

#### 匿名函数

对于数学函数表达式，最好采用匿名函数的方式进行封装，这里使用python类比。

对于二元函数
$$
f(x,y)=sinx+cosy-tanxy
$$
python

```python
import math
f = lambda x,y: sin(x)+cos(y)-tan(x*y)
```

matlab

```matlab
f=@(x,y)sin(x)+cos(y)-tan(x.*y);
```

上述匿名函数大多用于输入自变量向量，获取函数值向量，进而进行下一步计算或者绘图。

matlab提供syms方法用于求解方程组或者创建匿名函数矩阵。

示例一：方程组
$$
\begin{cases}\frac{x}{\cos y} +\frac{y}{\cos x} =0
 \\\sin x\cos y=1
\end{cases}
$$

```matlab
syms x y
eqn1=x./cos(y)+y./cos(x)==0;
eqn2=sin(x).*cos(y)==1;
```

示例二：函数矩阵
$$
\begin{bmatrix}
 1 & 1 & 1\\
 x & y & z\\
 x^{2}  & y^{2} &z^{2}
\end{bmatrix}
$$

```matlab
syms x y z
N=[1,1,1;x,y,z;x.^2,y.^2,z.^2];
```

#### 封装

封装函数进行模块化编程，有利于故障调试。在进行求解微分方程组或求解非线性规划模型时，需要传入匿名的函数进行求解，此时需要自行封装。

以求均值和标准差为例

python

```python
def stat(x):
    n=len(x)
    m=np.sum(x)/n
    s=np.sqrt(np.sum(x-m)^2/n)
    return m,s
```

matlab

```matlab
function [m,s] = stat(x)
    n = length(x);
    m = sum(x)/n;
    s = sqrt(sum((x-m).^2/n));
end
```

调用函数时可以选取想要的返回值进入工作区，以上述函数为例，如果只希望返回标准差，可采用如下方式调用函数：

```matlab
[~,s]=stat(x)
```

如需限制函数的输入变量，可使用arguments...end语句。

类似于python的try...except语句，如需捕捉报错并进行处理，可使用try...catch语句，在此不做叙述。

#### 面向对象*

类似于python可以用class定义类，可以使用matlab的classdef方法定义类属性和类方法。

语法如下：

```matlab
classdef (Attributes) ClassName < SuperclassNames
    properties (Attributes) ... end
    methods (Attributes) ... end
    events (Attributes) ... end
    enumeration ... end
end
```

其中classname为类名，‘< SuperclassNames’表示继承的类别，properties表示类属性，methods表示类方法。调用时须遵循classname.methods1...的链式调用原则。由于数学建模几乎不会应用面向对象编程，大多调用matlab内置类的方法，具体函数用法需读者自行查询文档。

## 常用数学计算

#### 初等数学

##### 求和与求积运算

matlab提供了sum求和函数和cumsum累计求和函数。sum函数默认按列求和，可使用sum(a,2)按行求和。cumsum默认按列累计求和。同理，可以使用prod和cumprod函数进行求积和累计求积运算。

```matlab
>> a=[2,3;3,4;1,5]

a =

     2     3
     3     4
     1     5

>> sum(a)

ans =

     6    12

>> sum(a,'all')

ans =

    18

>> cumsum(a)

ans =

     2     3
     5     7
     6    12

```

##### 求余和舍入

c语言可以使用"%"进行求余数运算，matlab使用mod或者rem函数进行求余数运算。

除数与被除数符号相同时，A%B=mod(A,B)=rem(A,B)。符号不同时，mod函数符号与被除数相同，rem函数符号与除数相同。

**mod(A,0)=A,rem(A,0)=NaN**

舍入函数：ceil：向上舍入；floor：向下舍入；round：四舍五入（可使用round(x,N)控制舍入位数）

##### 三角函数

所有的三角函数都有对应封装好的函数可以直接调用，以正弦为例：

sin()：正弦；asin()：反正弦；sinh()：双曲正弦；asinh()：反双曲正弦；以此类推。

实用的转换函数：

弧度转角度：rad2deg()；角度转弧度：deg2rad()

直角坐标系转极/柱坐标系：cart2pol()，相反方向转换pol2cart()

直角坐标系转球坐标系：cart2sph()，相反方向转换sph2cart()

##### 指数与对数函数

exp()：e指数；log()：自然对数

除了上述常用函数，matlab还内置了求质因数、最小公因数、最大公倍数、排列组合等众多函数，读者如在实际建模过程中需要，请自行查阅文档。

##### 分段函数

在自己封装函数的过程中，常常遇到分段函数，这个时候一般采用if语句的形式：

示例：
$$
f(x)=\begin{cases}e^{x},x\le 0
 \\cosx,x>0

\end{cases}
$$

```matlab
function y=func(x)
if x<=0
   y=exp(x);
else
   y=cos(x);
end
end
```

在使用syms方法时，也可以使用piecewise函数生成分段函数：

```matlab
syms x
y=piecewise(x<0,exp(x),x>0,cos(x));
```

绝对值函数:abs(x)

#### 统计数学

最大值：max()；最小值：min()

均值：mean()；中位数：median()；众数：mode()

标准差：std()；方差：var()；均方根：rms()

**上述函数对矩阵运算默认均为按列求值。**

##### 协方差矩阵

一般对两个向量计算协方差矩阵，计算结果含义如下：
$$
C=\begin{bmatrix}
 cov\left ( A,A \right )  &  cov\left ( A,B \right )\\
  cov\left ( B,A \right ) & cov\left ( B,B \right )
\end{bmatrix}
$$

```matlab
A = [3 6 4];
B = [7 12 -9];
cov(A,B)

ans = 2×2

    2.3333    6.8333
    6.8333  120.3333
```

##### 相关系数矩阵

[R,p]=corrcoef(A,B)

计算两个向量之间的Pearson相关系数，R为相关系数矩阵，p为显著性检验p值矩阵，A、B为两个向量。

```matlab
A = randn(10,1);
B = randn(10,1);
[R,p]= corrcoef(A,B)

R =

    1.0000    0.1691
    0.1691    1.0000

p =

    1.0000    0.6405
    0.6405    1.0000
```

得到的相关系数矩阵一般使用热力图的方式进行可视化，具体方法见**绘图**部分。

除此之外，matlab还提供了计算其他种类相关系数的方法：

[R,p]=corr(A,B,'type',...'rows',...)

其中type字段后可选'Pearson'：皮尔逊相关系数；'Kendall'：肯德尔秩相关系数；'Spearman'：斯皮尔曼相关系数。rows字段可以选择计算要使用的行，默认为'all'。

```matlab
[R,p]= corr(A,B,'type','Spearman')

R =

    0.5394

p =

    0.1133

```

##### 数据归一化

**最大最小值归一化**

mapminmax(x,min,max)：将数据按如下方式归一化到min和max之间，一般是0到1之间。

需注意此函数按行进行归一化操作，如需按列计算需预先进行转置。
$$
x'=\frac{x-min(x)}{max(x)-min(x)}
$$

```matlab
nx=mapminmax(x,0,1)
```

**z-score标准化**
$$
x'=\frac{x-mean(x)}{\sigma}
$$


```matlab
nx=zscore(x)
```

对数据预先进行标准化可以消除量纲对数据的影响，从而继续进行回归拟合操作。

##### 概率分布与概率密度

累积分布函数（cdf）

以计算向量x的正态累积分布函数为例：
$$
F(x)=\frac{1}{\sqrt{2\pi }\sigma  } \int_{-\infty }^{x} e^{-\frac{(x-\mu )^{2}}{2\sigma ^{2}} } dx
$$

```matlab
mu = 1;
sigma = 5;
pd = makedist('Normal','mu',mu,'sigma',sigma);
x = [-2,-1,0,1,2];
y = cdf(pd,x)
```

其中makedist函数可以创建一个概率分布对象，第一个字段需输入概率分布名称，之后字段输入概率分布参数，可供创建的概率分布见[文档]([累积分布函数 - MATLAB cdf - MathWorks 中国](https://ww2.mathworks.cn/help/stats/prob.normaldistribution.cdf.html))。

概率密度函数（pdf）

以计算向量x的泊松概率密度函数为例：
$$
f(x)=\frac{\lambda ^{x}}{x!} e^{-\lambda }
$$

```matlab
lambda = 2;
pd = makedist('Poisson','lambda',lambda);
x = [0 1 2 3 4];
y = pdf(pd,x)
```

多元正态分布概率密度（mvnpdf）

调用方法：

```matlab
y=mvnpdf(X,mu,Sigma)
```

其中X，mu，sigma为维度相同的向量，返回值y为对应的概率密度值向量。

##### 检验函数附表

数学建模过程中可能会遇到的检验方法或者分析方法

| 检验方法     | 对应matlab函数(help方法或官网文档查询) |
| ------------ | -------------------------------------- |
| 符号检验     | signtest                               |
| 独立性检验   | crosstab                               |
| 方差齐性检验 | vartestn                               |
| 方差分析     | anova1                                 |
| 多重比较     | multicompare                           |
| 正态性检验   | kstest                                 |
| 平稳性检验   | adftest                                |
| 白噪声检验   | lbqtest                                |
| 方差膨胀因子 | vif                                    |

#### 拟合与预测

##### 拟合＋预测

多项式拟合函数：[p,S]=polyfit(x,y,n)

其中p为拟合多项式的系数向量，S为误差结构体，x为自变量向量，y为因变量向量，n为拟合阶数。

拟合后的方程满足如下形式：
$$
p(x)=p_{1}x^{n}+p_{2}x^{n-1}+\dots +p_{n-1}x^{2}+p_{n}x+p_{n+1}
$$
示例：

```matlab
x=1:1:7;
y=[2,8,16,29,50,72,63];
[p,S]=polyfit(x,y,5)
```

多项式计算函数：[ny,delta]=polyval(p,nx,S)

其中ny为预测结果，delta为标准误差，nx为带预测的自变量值。

```matlab
[ny,delta]=polyval(p,x,S)
```

除此之外，matlab还提供roots函数用来求解多项式函数的根，polyint计算多项式函数积分，polyder计算多项式微分，在此不多叙述。

##### 曲线拟合工具箱

![image-20231005150240292](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20231005150240292.png)

曲线拟合工具箱在数学建模过程中较为常用，提供了多种数据拟合方式，可以比较选择拟合优度最好的拟合方式进行数据拟合，除了拟合二维曲线，还可以拟合三维曲面。如图为上面数据的拟合结果。

##### 多元线性回归

[b,bint,r,rint,stats]=regress(y,X)

X为自变量矩阵，y为因变量向量。

返回值中需着重关注系数向量b和检验值向量stats。其中stats向量中包含其中包含R方统计量、F统计量及其 p 值，以及误差方差的估计值。

对于n维自变量矩阵，因变量向量的表达式为：
$$
y=b_{1}x_{1}+b_{2}x_{2}+\dots +b_{n}x_{n}
$$
示例：

```matlab
load hald
y = heat;
X1 = ingredients;
x1 = ones(size(X1,1),1);%添加残差项
X = [x1 X1]; %组合成新数据
[b,~,~,~,stats] = regress(y,X)
```

需注意regress函数默认不对残差进行拟合，如果需要对残差进行拟合，**需要自行初始化残差项**。

运行结果

```matlab
b =

   62.4054
    1.5511
    0.5102
    0.1019
   -0.1441


stats =

    0.9824  111.4792    0.0000    5.9830
```

故多元线性回归表达式为：
$$
y=1.5511x_{1}+0.5102x_{2}+0.1019x_{3}-0.1441x_{4}+62.4054
$$
stats第三项为p值，小于0.05的默认显著性水平，因此响应 `y` 和 `X` 中的预测变量之间存在显著的线性回归关系。

##### 主成分分析(PCA)和偏最小二乘回归(PLS)*

主成分分析(PCA)广泛应用于降维，偏最小二乘回归拟合(PLS)广泛应用于多对多回归拟合，由于这两种方法使用广泛且有严谨的使用步骤，强烈建议读者在理解原理后自行封装算法。这里只给出matlab内置函数的调用方法：

```matlab
[coeff,score,latent]=pca(X)
```

coeff为在主成分系数，score为主成分得分，latent为主成分方差。

```matlab
[XL,YL,~,~,~,~,~,stats]=plsregress(X,Y,ncomp)
```

X为自变量矩阵，Y为因变量矩阵，ncomp为主成分个数(多个成分可能超出充分拟合数据所需要的成分数量，但可以根据此拟合的诊断来选择具有更少成分的简单模型。)XL、YL为自变量和因变量的系数向量，stats为检验值向量，内容同多元线性回归。

更多可供选择的输入字段的返回结果请读者自行查询文档。

##### 时间序列ARIMA预测*

ARIMA预测基于自回归与差分模型，也有着复杂严紧的步骤，这里只列出需要用到的函数以及其作用：

| 函数名   | 作用                     |
| -------- | ------------------------ |
| arima    | 构建arima预测模型        |
| estimate | 根据建立好的模型进行预测 |
| aicbic   | 获取对应模型的aicbic值   |

#### 高等数学

##### 导数与差分

解析导数

一般使用syms方法求解解析导数和高阶导数

```matlab
syms x 
f=sin(x);
f2=diff(f)
f3=diff(f,2)

f2 =
 
cos(x)
 
 
f3 =
 
-sin(x)
```

使用syms方法可以求解高阶偏导数

```matlab
syms x y z;
diff(x^2+log(y)+sqrt(z),y,4)

ans =
 
-6/y^4

%求解混合偏导数
syms x y z;
f=x^2+log(y)+sqrt(z);
dx=diff(f,x,1)
dxdy=diff(dx,y,1)

dx =
 
2*x
 
 
dxdy =
 
0
```

差分

```matlab
X = [1 1 2 3 5 8 13 21];
Y = diff(X)

Y = 1×7

     0     1     1     2     3     5     8
```

数值导数

matlab没有直接计算数值导数的方法，需要将导数差分化后近似计算。
$$
\frac{\mathrm{d} y}{\mathrm{d} x} =\lim_{\Delta x \to 0} \frac{f(x+\Delta x)-f(x)}{\Delta x} =\lim_{\Delta x \to 0} \frac{diff(f(x))}{\Delta x} 
$$

```
dx=0.0001;
x=[0 0+dx];
f=@(x)sin(x);
dydx=diff(f(x))/dx

dydx = 1.0000
```

除此之外，还可以使用gradient函数求解梯度，请读者自行查阅文档。

##### 极限

使用syms方法和limit函数即可计算极限。

示例一：
$$
\lim_{x \to 0} \frac{sinx}{x}
$$

```matlab
syms x
y=sin(x)/x;
limit(y,x,0)

ans =
 
1
```

示例二：
$$
\lim_{x \to 0^{+}} arctan(x-\frac{1}{x}) 
$$

$$
\lim_{x \to 0^{-}} arctan(x-\frac{1}{x}) 
$$

```matlab
syms x
y=atan(x-1/x);
r=limit(y,x,0,'right')
l=limit(y,x,0,'left')

r =
 
-pi/2

l =
 
pi/2
```

##### 积分

不定积分

```matlab
syms x
f=log(x);
int(f)

ans =
 
x*(log(x) - 1)
 
```

数值积分

示例一：
$$
\int_{0}^{+\infty } e^{-x^{2}}(lnx)^{2}
$$

```matlab
fun = @(x) exp(-x.^2).*log(x).^2;
q = integral(fun,0,Inf)

q = 1.9475
```

示例二：
$$
\begin{align*}
 \\\iint _{s}\frac{r}{\sqrt{rcos\theta +rsin\theta } (1+rcos\theta +rsin\theta)^{2}}
\\s:0\le \theta \le \frac{\pi }{2} ,0\le r\le \frac{1}{sin\theta+cos\theta } 
\end{align*} 
$$

```matlab
fun = @(x,y) 1./( sqrt(x + y) .* (1 + x + y).^2 );
polarfun = @(theta,r) fun(r.*cos(theta),r.*sin(theta)).*r;
rmax = @(theta) 1./(sin(theta) + cos(theta));
q = integral2(polarfun,0,pi/2,0,rmax)

q = 0.2854
```

可以使用integral3函数计算三重积分，而更高维度的积分需要使用匿名函数+积分函数的方法[嵌套求解](https://ww2.mathworks.cn/help/matlab/ref/integral3.html)。

##### 零点与最值

求解函数零点

方法一：syms+solve

示例：
$$
x^{3}=-1
$$

```matlab
syms x
eqn=x.^3+1==0;
s=solve(eqn,x)

s =
 
                  -1
1/2 - (3^(1/2)*1i)/2
(3^(1/2)*1i)/2 + 1/2

s=solve(eqn,x,'real',true)

s = -1
```

方法二：

fzero函数：

示例
$$
sinx=0
$$

```matlab
f=@(x)sin(x);
x0=3;
x = fzero(f,x0)%在初始点附近查找

x0=[2 4];
x = fzero(f,x0)%在初始区间内查找
```

fzero函数可以求解自己封装的不定式函数的零点，相较solve更灵活。

求函数最值

示例：求解sin(x)在0到2\pi范围上的最大值

```matlab
f=@(x)-sin(x);
[x,fval] = fminbnd(f,0,2*pi)
```

需注意，fminbnd为求解范围内的最小值，若希望求解最大值，应自行给目标函数添加负号。

x为最值点，fval为最小值。

##### 解方程组

示例一：
$$
\begin{align*}
 \\2u^{2}+v^{2}=0
\\u-v=1
\end{align*} 
$$

```matlab
syms u v
eqns = [2*u^2 + v^2 == 0, u - v == 1];
vars = [v u];
[solv, solu] = solve(eqns,vars)
```

示例二：
$$
\begin{align*}
 \\x^{2}+y^{2}+xy<1
\\x>0,y>0
\end{align*} 
$$

```matlab
syms x y
eqn1 = x > 0;
eqn2 = y > 0;
eqn3 = x^2 + y^2 + x*y < 1;
eqns = [eqn1 eqn2 eqn3];

S = solve(eqns,[x y],'ReturnConditions',true);
S.x,S.y
```

solve是一个非常强大的求解器，除了可以求解方程组、不等式组，还可以求解解具有周期性的方程。当方程非常复杂时，solve函数的精度会下降，matlab还提供了很多的方程求解器，读者可以自行查询文档。

##### 解常微分方程

解析解（dsolve)

示例一：无约束微分方程
$$
{y}'' +2{y}' +y=x^{2}
$$

```matlab
y=dsolve('D2y+2*Dy+y-x^2=0','x')
```

y = 
$$
x^2 -4\,x+C_1 \,{\mathrm{e}}^{-x} +C_2 \,x\,{\mathrm{e}}^{-x} +6
$$
示例二：有初值微分方程
$$
\begin{align*}{y}'' +4{y}' +29y=0
 \\y(0)=0,{y}'' (0)=15

\end{align*}
$$

```matlab
y=dsolve('D2y+4*Dy+29*y','y(0)=0,Dy(0)=15','x')
```

y =
$$
3\,\sin \left(5\,x\right)\,{\mathrm{e}}^{-2\,x}
$$
数值估计解

以最为常用的ode45函数为例：
$$
\begin{align*}{y}''' +5{y}'' {y}' -{y}'y=sinx
 \\y(0)=0,{y}'(0)=1,{y}''(0)=-1

\end{align*}
$$
求解复杂微分方程问题，需自行封装函数进行求解，代码如下：

```matlab
[x,y]=ode45(@cal,[-5 5],[0;1;-1]);
plot(x,y(:,1));
 

function res=cal(x,y)
    res = zeros(3,1);
    res(1)=y(2);
    res(2)=y(3);
    res(3)=-5*y(3)*y(2)+y(1)*y(2)+sin(x);
end
```

可以注意的y是一个因变量向量，其各个分量分别对应原函数，一阶导数和二阶导数，res为返回的求解向量，其分别对应被求解函数的一到三阶导数。@cal表示ode45的输入值是一个匿名函数，[-5 5]为求解区间，[0;1;-1]为y这个因变量向量的初值条件。

经过绘制，y(x)在[-5 5]的图像如下：

![img](file:///C:\Users\lenovo\AppData\Local\Temp\ConnectorClipboard7828624512766463206/image16965088181510.png)

选择ode求解器

| 求解器        | 问题类型 | 精度             |
| ------------- | -------- | ---------------- |
| ode45(最常用) | 非刚性   | 中               |
| ode23         | 非刚性   | 低               |
| ode113        | 非刚性   | 中到高(容差严格) |
| ode78         | 非刚性   | 高               |
| ode89         | 非刚性   | 高               |
| ode15s        | 刚性     | 中到高           |
| ode23s        | 刚性     | 低               |
| ode23t        | 刚性     | 低               |
| ode23tb       | 刚性     | 低               |
| ode15i        | 完全隐式 | 低               |

matlab提供了示例讲解不同求解器的适用范围和用法，请读者自行查阅文档。

##### 解偏微分方程*

matlab提供了pdepe函数计算偏微分方程的数值解，但由于求解要求的偏微分方程形式以及边界条件非常苛刻，建议根据物理模型先将偏微分方程简化为常微分方程，再使用求解常微分方程的方法进行求解。

pdepe求解的偏微分方程的标准形式：
$$
c(x,t,u,\frac{\partial u}{\partial x} )\frac{\partial u}{\partial t} =x^{-m}\frac{\partial }{\partial x} (x^{m}f(x,t,u,\frac{\partial u}{\partial x} ))+s(x,t,u,\frac{\partial u}{\partial x} )
$$
pdepe求解的偏微分方程的边界条件的标准形式：
$$
p(x,t,u)+q(x,t)f(x,t,u,\frac{\partial u}{\partial x} )=0
$$
pdepe的调用格式为：

```matlab
sol=pdepe(m,@pdefun,@icfun,@bcfun,xmesh,tspan)

function [c,f,s] = pdefun(x,t,u,dudx)
function u0 = icfun(x)
function [pl,ql,pr,qr] = bcfun(xl,ul,xr,ur,t)
```

其中m为坐标类型，0为标准坐标系，1为柱坐标系，2为球坐标系。

pdefun为待求解的偏微分方程，c,f,s即为标准形式中的三个函数，需用x,t,u,dudx表示出来。

icfun为初值条件函数，u0为初始时刻函数u的值。

bcfun为边界条件函数，xl，ul分别为x和u的左边界，xr，ur分别为x和u的右边界。pl，ql是边界条件的标准形式中p、q两函数的左边界，pr，qr为右边界，这个函数非常难写。

xmesh为自变量向量，tspan为时间向量，两者组成了解网格。

#### 计算几何*

多边形面积

s=polyarea(x,y)：x，y为坐标分量向量，s为x和y组成的坐标围成的多边形的面积。

判断点是否在多边形内部

in=inpolygon(xq,yq,xv,yv)

xq,yq为需要判断的点的横纵坐标向量，xv，yv为围成多边形的点的横纵坐标向量。in为是否在多边形内部的逻辑数组。根据返回的逻辑数组可以通过逻辑索引可以找到在多边形内部的点。

具体例子见[文档](https://ww2.mathworks.cn/help/matlab/ref/inpolygon.html)。

#### 图论*

由于本人多用python解决图论相关问题，对matlab相关函数并不熟悉，这里只给出函数作用表，供读者自行查阅：

| 函数名      | 作用         |
| ----------- | ------------ |
| graph       | 生成无方向图 |
| digraph     | 生成有方向图 |
| bfsearch    | 广度优先搜索 |
| dfsearch    | 深度优先搜索 |
| minspantree | 最小生成树   |
| distances   | 最短路径搜索 |

#### 插值

常用的三种平滑的差值方法

| 插值方法                         | 函数名 |
| -------------------------------- | ------ |
| 分段三次 Hermite 插值            | pchip  |
| 修正 Akima 分段三次 Hermite 插值 | makima |
| 三次样条数据插值                 | spline |

```matlab
x = -3:3; 
y = [-1 -1 -1 0 1 1 1]; 
xq1 = -3:.01:3;
p = pchip(x,y,xq1);
s = spline(x,y,xq1);
m = makima(x,y,xq1);
plot(x,y,'o',xq1,p,'-',xq1,s,'-.',xq1,m,'--')
legend('Sample Points','pchip','spline','makima','Location','SouthEast')
```

对应结果：

[img](https://ww2.mathworks.cn/help/matlab/ref/datainterpolationcomparisonexample_01_zh_CN.png)

#### 聚类

常用的聚类算法有三种：系统聚类：通过计算类与类之间的距离确定类别数，优点精度高，缺点速度慢，高维数据难以聚类。K-means聚类：应用广泛，根据均值进行聚类，计算简单，但聚类效果容易受簇的数量影响，精度不高。dbscan聚类：根据密度聚类，精度很高，但是计算速度慢。

| 函数名     | 用法                         |
| ---------- | ---------------------------- |
| pdist      | 计算成对观测值之间的两两距离 |
| linkage    | 生成层次聚类树               |
| dendrogram | 生成聚类树状图               |

kmeans调用方法：

```matlab
[idx,C]=kmeans(X,k)
```

其中idx为簇索引，可以利用逻辑索引分离不同簇的数据，C为各个簇的质心坐标。

X为预聚类矩阵，k为簇的数量。

dbscan调用方法：

```matlab
idx=dbscan(X,epsilon,minpts)
```

其中epsilon为搜索半径，越小精度越高速度越慢，minpts为最少聚类数。

示例：

```matlab
rng('default')
N = 300;
r1 = 0.5;
r2 = 5;
theta = linspace(0,2*pi,N)';
X1 = r1*[cos(theta),sin(theta)]+ rand(N,1); 
X2 = r2*[cos(theta),sin(theta)]+ rand(N,1);
X = [X1;X2];
idx = dbscan(X,1,5);
kidx = kmeans(X,2);
```

## 优化求解器

针对不同的问题，应选用不同的优化求解器和算法，以下是针对问题的优化决策表。

| 约束条件类型                | 线性目标函数   | 二次型目标函数 | 最小二乘型目标函数 | 平滑非线性目标函数 | 非平滑非线性目标函数 |
| --------------------------- | -------------- | -------------- | ------------------ | ------------------ | -------------------- |
| 无                          |                | quadprog       | lsqnonlin...       | fminsearch...      | fminsearch           |
| 边界                        | **linprog**    | quadprog       | lsqnonlin...       | fminbnd...         | fminbnd              |
| 线性                        | **linprog**    | quadprog       | lsqnonlin...       | **fmincon**...     |                      |
| 锥                          | coneprog       | **fmincon**    | **fmincon**...     | **fmincon**...     |                      |
| 非线性光滑                  | **fmincon**    | **fmincon**    | **fmincon**...     | **fmincon**...     | **fmincon**...*      |
| 离散条件(整数规划、0-1规划) | **intlinprog** |                |                    | **ga***            | **ga***              |

##### 线性规划求解器linprog

简单的线性规划和整数规划、0-1规划建议使用lingo进行求解，详情见lingo教程。

linprog能够求解的优化问题类型严格满足如下标准形式：
$$
\begin{align*}min\space f^{T}x
 \\s.t.\begin{cases}A\cdot x\le b
 \\Aeq\cdot x=beq
\\lb\le x\le ub
\end{cases}

\end{align*}
$$
调用代码：

```matlab
[x,fval]=linprog(f,A,b,Aeq,beq,lb,ub)
```

其中x是决策变量向量，f是目标函数的系数向量，fval是目标函数的最优值，A是小于等于条件的系数矩阵，b是小于等于条件的约束条件向量，Aeq是恒等于条件的系数矩阵，beq是恒等于条件的约束向量。lb、ub是决策变量向量的下界和上界向量。

这么说比较抽象，下面给出示例：

求解如下形式的线性优化问题：
$$
\begin{align*}max\space  {\textstyle -\sum_{i=1}^{6}x_{i}} 
 \\s.t.\begin{cases} 3x_{1}+5x_{3}-2x_{4}\le 6
\\x_{1}-2x_{2}+x_{6}\ge 2
 \\x_{1}+x_{2}-x_{4}=-2
\\x_3-x_{5}=0
\\x_{i}\ge 0
\end{cases}

\end{align*}
$$
将目标函数和约束条件都写成矩阵乘法形式：

**需注意，matlab所有的优化求解器都只能求解最小型问题，如果是最大型问题需要取负值转化为最小型问题。**
$$
\begin{align*}min\space  \begin{bmatrix}
1  & 1 & 1 & 1 & 1 &1
\end{bmatrix}\cdot x
 \\s.t.\begin{cases} 
\begin{bmatrix}
 3 & 0 & 5 & -2 & 0 & 0\\
 -1 & 2 & 0 & 0 & 0 &-1
\end{bmatrix}x\le \begin{bmatrix}6
 \\-2

\end{bmatrix}

\\\begin{bmatrix}
 1 & 1 & 0 & -1 & 0 & 0\\
 0 & 0 & 1 & 0 & -1 &0
\end{bmatrix}x=\begin{bmatrix}-2
 \\0

\end{bmatrix}
\\\begin{bmatrix}
 0 & 0 & 0 & 0 & 0 &0
\end{bmatrix}^{T}\le x\le \begin{bmatrix}
 +\infty  & +\infty  & +\infty  & +\infty  & +\infty  &+\infty 
\end{bmatrix}^{T}
\end{cases}

\end{align*}
$$
故代码为：

```matlab
f=[1 1 1 1 1 1];
A=[3,0,5,-2,0,0;-1,2,0,0,0,-1];
b=[6,-2];
Aeq=[1,1,0,-1,0,0;0,0,1,0,-1,0];
beq=[-2,0];
lb=[0 0 0 0 0 0];
[x,fval]=linprog(f,A,b,Aeq,beq,lb)
```

求解结果如下：

```matlab
Optimal solution found.
x = 6×1    
     0
     0
     0
     2
     0
     2

fval = 4
```

##### 整数、0-1规划求解器intlinprog

intlinprog函数与linprog能求解的优化问题满足的标准形式相似，不过增加了可供选择的离散型约束条件，其调用方法如下：

```matlab
[x,fval]=intlinprog(f,intcon,A,b,Aeq,beq,lb,ub,x0)
```

其中intcon为离散型决策变量向量，填写需要限制为整数型的决策变量的下标。x0为初始点向量，提供一个合理的初始点可以更快的找到最优值，不提供初始点则随机初始点查找。

示例：
$$
\begin{align*}
\\min \space -3x_{1}-2x_{2}-x_{3}
\\\begin{cases}x_{1}+x_{2}+x_{3}\le 7
 \\4x_{1}+2x_{2}+x_{3}=12
 \\x_{1},x_{2}\ge 0,x_{3}=0\space or\space 1

\end{cases}
\end{align*}
$$
依然是将目标函数与约束条件写成矩阵乘法形式，这里不再赘述。下面仅谈论0-1变量的处理方法，

在这个问题中x{3}为0-1变量，故先设置x{3}为整型变量，在设置上界为1。

完整代码：

```matlab
f = [-3;-2;-1];
intcon = 3;%设置x3为整型，如果是x2和x3，那么有intcon=[2,3]
A = [1,1,1];
b = 7;
Aeq = [4,2,1];
beq = 12;
lb = zeros(3,1);
ub = [Inf;Inf;1];
x = intlinprog(f,intcon,A,b,Aeq,beq,lb,ub)
```

由于大家都不是很喜欢矩阵乘法，matlab还提供了结构体解决优化问题的方法，这里仅对这个题给出示例：

```matlab
x = optimvar('x',2,'LowerBound',0);
xb = optimvar('xb','LowerBound',0,'UpperBound',1,'Type','integer');
prob = optimproblem('Objective',-3*x(1)-2*x(2)-xb);
cons1 = sum(x) + xb <= 7;
cons2 = 4*x(1) + 2*x(2) + xb == 12;
prob.Constraints.cons1 = cons1;
prob.Constraints.cons2 = cons2;
problem = prob2struct(prob);
[sol,fval,exitflag,output] = intlinprog(problem)
```

由于要设置大量的参数，笔者并不觉得这种方法很实用。

##### 通用优化求解器fmincon

fmincon函数无疑是matlab最实用的函数，它可以解决大部分的优化问题，当你无法判断自己建立的优化模型适合用什么求解器求解，那几乎可以无脑选择fmincon，支持匿名函数使得你几乎可以封装任意形式的目标函数与约束变量进行求解。

fmincon求解的优化问题需严格按照以下标准形式：
$$
\begin{align*}min\space f(x)
 \\s.t.\begin{cases}c(x)\le 0
 \\ceq(x)=0
 \\A\cdot x\le b
 \\Aeq\cdot x=beq
\\lb\le x\le ub
\end{cases}

\end{align*}
$$
基本调用形式：

```matlab
[x,fval]=fmincon(@optfun,x0,A,b,Aeq,beq,lb,ub,@nonlcon,options)
```

其中@optfun是目标函数的匿名函数，@nonlcon是非线性约束条件的匿名函数，options是可供能够选择的字段。

@optfun可以是直接列出的匿名函数，例如：

```matlab
optfun=@(x)100*(x(2)-x(1)^2)^2 + (1-x(1))^2;
```

或者自己封装的函数：

```matlab
function y=optfun(x)
...
y=...;
end
```

需要注意x是决策变量向量，在编写目标函数和非线性约束条件时，需要对x的分量进行分离运算。

在编写目标函数时，可以提供梯度矩阵加速运算，同时在调用fmincon时添加相应的options选项，方法如下：

```matlab
function [f,grad]=optfun(x)
...
f=...;
if nargout>1
   grad=[...];
end
end

options = optimoptions('fmincon','SpecifyObjectiveGradient',true);
x = fmincon(@optfun,x0,A,b,Aeq,beq,lb,ub,@nonlcon,options)
```

示例：

```matlab
function [f,g] = rosenbrockwithgrad(x)
% Calculate objective f
f = 100*(x(2) - x(1)^2)^2 + (1-x(1))^2;

if nargout > 1 % gradient required
    g = [-400*(x(2)-x(1)^2)*x(1)-2*(1-x(1));
        200*(x(2)-x(1)^2)];
end
```

对于@nonlcon函数，封装时应满足如下格式：

```matlab
function [c,ceq]=nonlcon(x)
...
c=[...];
ceq=[...];
end
```

对于多个非线性约束条件，可以采用返回向量的方法。

示例：
$$
\begin{align*} lnx_{i}-sinx_{i}\ge 1,i=1\dots 7
 \\\left \lfloor x_{i}+x_{i+1} \right \rfloor =4,i=1\dots 6
\\if\space x_{3}\ge 3,x_{4}\ge x_{7}
\end{align*}
$$
对于相同构造的非线性约束条件，可以采用循环结构赋值返回c和ceq，对于约束条件中存在if语句的情况，视作分段函数处理，统一放在@nonlcon函数中。

```matlab
function [c,ceq]=nonlcon(x)
c=zeros(8,1);
ceq=zeros(6,1);
for i=1:7
    c(i)=sin(x(i))-log(x(i))+1;
end
if x(3)>=3
    c(8)=x(7)-x(4);
else
    c(8)=x(4)-x(7);
end
for i=1:6
    ceq(i)=floor(x(i)+x(i+1))-4;
end
end
```

对于非线性约束条件，也可以使用罚函数的方式添加在目标函数中，将约束优化问题转化成无约束优化问题，这样就可以有更多的方法进行求解，比如fminsearch求解器，粒子群算法，模拟退火算法等。

fmincon的options中有众多可选项，除了上面介绍的梯度选项，还有"Algorithm"(算法)，"FiniteDifferenceStepSize"(有限差分步长)，"StepTolerance"(终止容差)等选项，有时候在求解过程中可能求得了一个局部最优解或者达到终止容差或者迭代次数终止了，可以调节options中的参数改善结果。

##### 遗传算法优化求解器ga*

相较于fmincon，遗传算法求解器ga可以求解带有整数或者0-1条件的复杂非线性优化问题，精度也较高，不过求解速度往往更慢。

其调用形式如下：

```matlab
[x,fval]=ga(@objfun,nvars,A,b,Aeq,beq,lb,ub,@nonlcon,intcon,options)
```

nvars是变量数，即length(x)。@objfun，@nonlcon方法与fmincon一致，intcon用法与intlinprog用法一致，这里不再赘述。

遗传算法往往需要绘制种群适应度曲线，可以在options中选择此选项进行实时绘制：

```matlab
options = optimoptions('ga','PlotFcn', @gaplotbestf);
```

除此之外options还可以设置遗传算法的相关参数，请读者了解遗传算法的基本原理后查询官方文档设置，笔者一般使用默认设置。

##### 多目标遗传算法优化求解器gamultiobj*

在建模时应避免建立多目标优化模型，如果一定要建立该模型，建议使用线性权重法或者其他方法转化为单目标优化问题求解。

gamultiobj求解器的好处在于对于双目标优化问题可以实时画出Pareto最优曲线，需注意该函数返回的最优解一般为多个。

其调用形式如下：

```matlab
[x,fval]=gamultiobj(@objfun,nvars,A,b,Aeq,beq,lb,ub,@nonlcon,intcon,options)
```

其中@objfun需返回函数向量，以双目标优化问题为例：

```matlab
function f=optfun(x)
f=zeros(2,1);
...
f(1)=...;
f(2)=...;
end
```

同样的options中可以设置诸多参数，笔者借鉴该[作者的文章](https://blog.csdn.net/panmingqian/article/details/122416880)，建议采用如下参数设置：

```matlab
options = gaoptimset('ParetoFraction',0.3,'PopulationSize',100,'Generations',200,'StallGenLimit',200,'TolFun',1e-100,'PlotFcns',@gaplotpareto);
```

除此之外，matlab还提供了粒子群算法求解器particleswarm和模拟退火算法求解器simulannealbnd，感兴趣的读者请自行查询文档。

## 绘图

