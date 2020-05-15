---
title: python基础
tags:
  - python
  - notes
abbrlink: 81b2f4bf
date: 2020-04-03 12:00:00
---

## python基础

python官方网站 [python](https://www.python.org)  

#### 特点
1. 数值没有长度的限制  
2. 可以直接执行运算  
3. 使用缩进的空白代替大括号{}，缩进的空白数量是可变的，但所有代码块语句必须包含相同的缩进空白数量

#### 注释
单行注释 #
多行注释 """ """或 ''' '''

#### 字符串
''' '''  或  ' '  或 " "

| 作用| 函数名  |
|--|--|
| 查找  | find()  |
| 分割 |split()  |
| 大小写转换 |upper()/lower()  |
|首字母大写|title()   |
|截取 |变量名[首:尾] (左闭右开)  |
|   追加 |append()|
|  取反 |reverse()  |
|替换 |replace('要替换的字符','替换的字符')  |


<!--more-->

#### 变量

在内存中存储的数据可以有多种类型
python五个标准的数据类型：  

* Numbers（数字）
    * int 整型
	* long int 长整型（在python3中统一为int整形）
	* e 科学计数法
* String（字符串）
* List（列表）
* Tuple（元组）
* Dictionary（字典）

变量在定义时，没有指定的数据的类型，定义变量的一般格式为：
1. 变量名=值；  
2. 变量1，变量2，··· 变量n=值1，值2，··· 值n （使用此种方式定义变量时，左右的变量个数和值必须保持一致）  

数据类型转换
- 整型 int('数值')  数值必须为int类型才能转换 
- 单精度浮点数 float('数值')  
- 字符串 str(数值)  

#### 运算符  
###### 算术操作符  
- 加 +  
- 减 -  
- 乘 *  
- 除  /   
- 整除 //  
- 求余 %  
- 幂运算 **  m**n：n个m相乘  

###### 逻辑运算符
与 and 
或 or 
非 not  

###### 成员运算符  
in / not in 判断元素是否存在某个序列中  

###### 身份运算符  
is / is not 判断两个标识符是否引用自同一个对象/不同对象

#### python常用函数  
###### 输入函数
variable=input()
###### 输出函数  
print("",end='\r\n') 输出函数  默认以\r\n结尾 end参数可以调整print的输出结尾 
print("字符串" * 数值n) 将字符串输出n次 
print(数值a 算术运算符 数值b) 执行运算并输出结果   

###### type函数  
type(数据) 能得到数据的数据类型  

###### isinstance函数  
isinstance(数据,数据类型) 判断数据是否为所给的数据类型  

###### id函数  
id(数据) 返回所给数据在内存中的地址  

#### 语句  
###### 条件语句  
```python
if 条件语句1 :  
  执行语句1  
elif 条件语句n :  
  执行语句n  
else :  
  执行语句n+1  
```

###### 循环语句
```python
#while循环语句
while 条件语句 :  #条件符合
  循环体语句  
else :  	#条件不符合
  执行语句  
#for循环语句
#遍历列表
for 变量名 in 列表名:
  执行语句
else :
  执行语句
```
- break：中断循环，跳出循环体  
- continue：循环向下一个执行  
- pass：不执行任何操作，起到占位的作用 

##### lambda表达式
lambda表达式不是函数，是实现简化语句的一种形式
```python
#eg.
sum = lambda 参数 : 执行语句
```


#### 列表list
- 列表中可存储不同的数据类型   
- 列表元素位置排序从0开始，可以用负数访问列表，最后一个元素的位置为-1 
组合 不同的列表可以组合成一个列表，用+链接 
重复 同一个列表重复n次，用*n实现  

###### 列表函数
比较 cmp() （2.x版本适用）在3.x版本中以(a>b)>(a<b)替代 
元素是否存在于列表 in关键字 
删除 del 
长度 len()
最大值 max()
最小值 min()
将元组转换成列表 list()  

###### 列表方法
追加 append()
扩展 extend(seq)
插入 insert(index,object)
索引 index(object)
元素出现的次数 count(object)
移除指定位置的元素 pop([index=-1])
移除匹配元素的第一个 remove(object)
反向 reverse() / 列表名[::-1] 
排序 sort(cmp=none,key=none,reverse=false) cmp指定方法,key指定元素,reverse:true降序,false升序  

#### 元组tuple
- 与列表类似,元组中的元素值不能改变
- 元组中的元素不允许删除,只能删除整个元组
- 元组使用小括号,列表使用大括号
- 创建空元组 tup=() 当元组中只包含一个元素时,要以逗号结尾
###### 元组函数
比较 cmp()
长度 len(tuple)
最大值 max(tuple)
最小值 min(tuple)
列表转换成元组 tuple(seq)

#### 字典dictionary
-  字典是一种可变容器模型，且可存储任意类型对象
-  字典是以键值对的形式存在的key:value
-  键一般是唯一的，如果重复最后的一个键值对会替换前面的，值不是唯一的
-  值是可以取任何数据类型,既可以是python的标准对象，也能是用户自定义对象
-  键必须不可变，可用数字，字符串，元组，但不能用列表
-  访问字典中不存在的键，会输出错误KeyError
-  字典的内容可以增添新的键/值对，修改或删除已有的键/值
-  删除键值对 del(字典名['键名'])
-  删除字典 del(字典名)

###### 字典函数
比较两个字典元素 cmp(dict1,dict2)   ~~3.x不可用~~
计算字典元素个数 len(dict)
输出字典可打印的字符串表示 str(dict)
返回输入的变量类型 type(variable)  

###### 字典方法
- {key1:value1,key2:value2}
- 返回一个字典的复制 dict.copy()
- 创建以指定序列的元素为键,value为初始值的新字典 dict.fromkeys(seq[,value])
- 返回指定键的值 dict.get(key,default=None) 	当值不在字典时返回default值
- 字典的键查询 dict.has_key(key)
- 以列表返回可遍历的元素、数组 dict.items()  
- 查询所有键名 dict.keys()
- 以列表返回字典中的所有值 dict.values()
- 设置键的值 dict.setdefault(key,default=None)  当键不存在时，自动创建
- 更新字典的键值对 dict1.update(dict2)
- 删除 pop(key[,default]) 返回被删除的键值
- 随机返回并输出一对键值对 dict.popitem()

#### set
- python内置数据结构（集合）
- 无序，可更改  
- set集合类需要的参数必须是迭代器类型的  
- 一般用来去重，集合的求交集或并集  

#### 函数
函数的定义：
```python
def 函数名(参数) :	#当参数是列表，字典等引用类型数据，函数内的执行语句会对其值改变
    执行语句
    return 返回值
```
函数的参数
- 必备参数
- 关键字参数
- 默认参数
- 不定长参数 def 函数名(*参数名)

#### 异常
```python
try:
	执行可能抛出异常的语句
except:
	异常处理语句
finaly:
	是否发生异常都执行的语句
```

#### 模块
import关键字导入模块，使python文件可供同一目录下的其他python文件使用
调用函数: 

```python
#1
import 模块名
模块名.函数名

#2
from 模块名 import 函数名	#导入指定函数

#3
from 模块名 import *	#导入模块下所有函数
```
起别名 import 模块名 as 别名   

###### __ main __ 与 __ name __  
判断当前是否是主线程  

###### 搜索路径
```python
import sys
sys.path	#返回路径
sys.path.append(路径)		#追加需要导入的模块路径:本地路径
```

#### 类class
class 类名
&nbsp;&nbsp; 属性
&nbsp;&nbsp; 函数
&nbsp;&nbsp; def __ init __(self,属性) #构造函数，self是类的实例  

&nbsp;&nbsp; 伪私有属性的访问:  类对象名._类名__属性名

###### 继承
```python
class B:
    def __init__(self):
        pass
#A继承B
class A (B):
    def __init__(self,attribute):
        super(B, self).__init__(attribute)
        
```

#### 文件
###### 文件的读写 
调用格式 f = open(filename, mode,buffering)
filename:文件的路径
mode：模式  
- w 写
- r 读
- a 追加  

（b----二进制编码）不同模式可以混合
向文件中写入f.write(bytes)
文件读写完后要关闭  f.close


#### 工具
###### bs4/beautiful soup
替代正则
[beautiful soup](https://beautifulsoup.readthedocs.io/zh_CN/latest)

###### tkiner
基于视图化ui的模块库

###### phantomJS

PhantomJS是可使用JavaScript编写脚本的无头Web浏览器。它可以在Windows，macOS，Linux和FreeBSD上运行。  

使用QtWebKit作为后端，它为各种Web标准提供了快速的本机支持：DOM处理，CSS选择器，JSON，Canvas和SVG。  

###### selenium/webdriver 
通过模拟浏览器进行网络爬虫的库

###### requests
网络爬虫相关库，适用于静态网页页面的爬取

###### PIL/Image, ImageTk
图片处理库
