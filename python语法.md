---
title: python语法
tags:
  - python
  - notes
abbrlink: 81b2f4bf
date: 2020-04-03 12:00:00
---

## python语法

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
print("%d" % 变量名[int]) 输出变量值 [%s --字符串]

###### type函数
type(数据) 能得到数据的数据类型

###### isinstance函数
isinstance(数据,数据类型) 判断数据是否为所给的数据类型

###### id函数  
id(数据) 返回所给数据在内存中的地址

###### eval函数

将字符串当成有效的表达式来求值并返回计算结果

```python
cal = input("请输入一个算术式")
print(eval(cal))
```

> 请输入一个算术式3\*2
> 6
>
> Process finished with exit code 0

eval直接转换input

> \_\_import\_\_(’os‘).system(’ls‘)
>
> 等价代码
>
> ```python
> import os
> os.system("终端命令")
> ```
>
> 执行成功，返回0
> 执行失败，返回错误信息

#### 语句
##### 条件语句

###### 标准写法

```python
if 条件语句1 :  
  执行语句1  
elif 条件语句n :  
  执行语句n  
else :  
  执行语句n+1  
```

###### 三元表达式

variable= value1 if 条件语句1  else  value2

```python
a = 3 if 3 < 2 else 2	# 2和3之间取较小值
```

##### 循环语句

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
| 函数名/关键字 | 作用                                              |
| :-----------: | :------------------------------------------------ |
|     cmp()     | 比较，（2.x版本适用）在3.x版本中以(a>b)>(a<b)替代 |
|      in       | 元素是否存在于列表                                |
|      del      | 删除列表                                          |
|     len()     | 列表长度                                          |
|     max()     | 列表中的最大值                                    |
|     min()     | 列表中的最小值                                    |
|    list()     | 将元组转换成列表                                  |

###### 列表方法

| 函数名                                                       | 作用                 |
| ------------------------------------------------------------ | -------------------- |
| append()                                                     | 追加                 |
| extend(seq)                                                  | 扩展                 |
| insert(index,object)                                         | 插入                 |
| index(object)                                                | 索引                 |
| count(object)                                                | 元素出现的次数       |
| pop(index=-1) 默认为最后一个元素                             | 移除指定位置的元素   |
| remove(object)                                               | 移除匹配元素的第一个 |
| reverse() / 列表名[::-1]                                     | 反向                 |
| sort(cmp=none,key=none,reverse=false) <br />cmp指定方法,<br />key指定元素,<br />reverse:true降序,false升序 | 排序                 |

#### 元组tuple
- 与列表类似,元组中的元素值不能改变
- 元组中的元素不允许删除,只能删除整个元组
- 元组使用小括号 (),列表使用方括号 []
- <font color="red">创建空元组 tup=() 当元组中只包含一个元素时,要以逗号结尾</font>
###### 元组函数

| 函数名     | 作用           |
| ---------- | -------------- |
| cmp()      | 比较           |
| len(tuple) | 长度           |
| max(tuple) | 最大值         |
| min(tuple) | 最小值         |
| tuple(seq) | 列表转换成元组 |

#### 字典dict
-  字典是一种可变容器模型，且可存储任意类型对象
-  字典是以键值对的形式存在的key:value
-  键一般是唯一的，如果重复最后的一个键值对会替换前面的，值不是唯一的
-  值是可以取任何数据类型,既可以是python的标准对象，也能是用户自定义对象
-  键必须不可变，可用数字，字符串，元组，但不能用列表
-  访问字典中不存在的键，会输出错误KeyError
-  字典的内容可以增添新的键/值对，修改或删除已有的键/值
-  删除键值对 del  字典名['键名']
-  删除字典 del 字典名

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

#### 元组和字典的拆包

拆包语法：简化元组/字典变量的传递

```python
def func(*args, **kwargs):
    print(args)
    print(kwargs)


gl_nums = {1, 2, 3, 4}
gl_dict = {"name": "ZhangSan", "age": 20}

# 元素均被传递为元组
func(gl_nums, gl_dict)
# 拆包语法
func(*gl_nums, **gl_dict)
# 一般形式
func(1, 2, 3, 4, name="ZhangSan", age=20)
```

#### 集合set
- python内置数据结构（集合）	
- {}
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
  - *参数名 接收元组
  - **参数名 接收字典

#### 异常

异常的完整处理方式

```python
try:
	# 执行可能抛出异常的语句
    pass
except 错误类型:
	# 异常处理语句
    pass
except Exception as result:
    # 打印错误类型
    print(result)
else:
    # 没有异常才会执行的代码
    pass
finally:
	# 是否发生异常都执行的语句
    pass
```

##### 简单异常捕获

```python
try:
    num = int(input("请输入整数"))
except:
    print("请输入正确的数字")
finally:
    print("*" * 50)
```

##### 错误类型捕获

不同错误类型进行捕获

```python
try:
    num = int(input("请输入整数"))
    res = 10 / num
    print(res)
except ValueError:	# 错误类型1
    print("请输入正确的数字")
except ZeroDivisionError: # 错误类型2
    print("0不能作被除数")
```

捕获未知错误

```python
try:
    num = int(input("请输入整数"))
    res = 10 / num
    print(res)
except Exception as result:
    print("未知错误%s" % result)
```

##### 异常的传递

当函数/方法执行出现异常，会将异常传递给函数/方法的调用一方

如果传递到主程序，仍没有异常处理，程序才会被终止

##### 主动抛出异常raise

抛出异常，由其他需要处理的函数捕获异常

```python
raise Exception("异常信息")
```

#### 类class
> class 类名:
> 	属性
> 	函数

其中函数中的`self`，哪一个对象调用的方法，`self`就指向哪一个对象

```python
class ClassName:
    def __init__(self,属性): # 构造函数，self是类的实例
         self.name = "Tom" # 初始化内部属性
```

一种给类的对象添加属性的方法，即在类的外部给对象增加属性，但<font color="red">不推荐</font>使用，因为没有对类进行实质性修改

```python
# 一种给类的对象添加属性的方法，但不推荐使用，因为没有对类进行实质性修改
class Test:
    pass


a = Test()
a.name = "A"
print(a.name) 
```

##### 私有属性和私有方法

> 私有属性或私有方法：
> 在属性或方法名前加 __ ,即为类的私有属性或方法

```python
class Women:
    def __init__(self, name):
        self.name = name
        # 私有属性
    	self.__age = 20

    def secret(self):
        print("%s的年龄是%d" % (self.name, self.__age))

    def __sing(self):
        pass


girl = Women("Mary")
# 私有属性在外界不能直接访问
# print(girl.__age)

girl.secret()
# 私有属性在外界不能直接访问
# girl.__sing()
```

> 伪私有属性的访问:  类对象名._类名__属性名

```python
print(girl._Women__age)
print(girl._Women__sing())
```

##### dir内置函数

<table>
    <caption align="top">常用内置方法及属性</caption>
    <th align="center">
    <td>序号</td>
    <td>方法名</td>
    <td>类型</td>
    <td>作用</td>
    </th>
<tr align="center">
    <td>01</td>
    <td>__new__</td>
    <td>方法</td>
    <td>创建对象时，会被自动调用</td>
</tr>
<tr align="center">
    <td>02</td>
    <td>__init__</td>
    <td>方法</td>
    <td>对象初始化时，会被自动调用</td>
</tr>
<tr align="center">
    <td>03</td>
    <td>__del__</td>
    <td>方法</td>
    <td>从内存中销毁对象前，会被调用</td>
</tr>    
<tr align="center">
    <td>04</td>
    <td>__str__</td>
    <td>方法</td>
    <td>返回对象的描述信息，print()函数输出使用</td>
</tr>
</table>

```python
# 查看dir内置函数
dir(func())
# ['__bool__', '__class__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__']

```

del关键字 销毁对象

```python
class A:
    def __init__(self):
        print("初始化")

    def __del__(self):
        print("销毁前")

    # print()默认输出16进制的地址值
    def __str__(self):
        # 必须返回一个字符串
        return "A"


a = A()
print(a)
del a
```

> 初始化
> A
> 销毁前
>
> Process finished with exit code 0

##### 继承

- 单继承：某个类只直接继承一个类
- 多继承：某个类直接继承多个类

> 注意：子类不能在自己的方法内部直接访问父类私有属性或私有方法，只能通过父类的公有方法间接调用
>
> 多继承父类之间不要有重名的属性或方法名，默认优先调用先继承的父类
>
> MRO：method resolution order 多继承时判断属性、方法的调用路径
> 子类类名.\_\_mro \_\_

- 重写：子类对父类的方法进行重写

  - 完全重写
  - 扩展重写：通过super().父类方法调用父类方法 python3.x
    父类类名.方法名(self) python2.x

  > super是一个特殊类
  > super()就是使用super类创建出来的对象

- 重载

```python
class B:
    def __init__(self):
        pass
#A继承B
class A (B):
    def __init__(self,attribute):
        super(B, self).__init__(attribute)	 
```

```python
class Person:
    __sex = None

    def eat(self):
        print("eating")

    def sleep(self):
        print("sleepping")


class Man(Person):
    __sex = "man"


class Women(Person):
    __sex = "female"


class Coder(Person):

    def code(self):
        print("coding")


class Boss(Man, Coder):

    def __init__(self):
        print("Manager")

    # override重写
    def code(self):
        super().code()
        Coder.code(self)	# python2.x中
        print("check")
```

##### 新式类与旧式(经典)类

新式类：以object为基类的类[推荐使用] python3.x没有指定父类，默认使用object作为类的基类

经典类：不以object为基类的类[不推荐使用] python2.x没有指定父类，不会以object作为基类

##### 类属性查找--向上查找

```python
class Tool(object):
    count = 0 # 类属性，相当于静态变量

    def __init__(self, name):
        self.name = name
        self.count += 1
```

- 首先在对象内部查找属性	对象名.属性 [不推荐]

  > 注意：对象名.属性 =value 只会给对象添加一个 属性，并不会影响到类属性的值

- 没有找到就向上查找类属性 类名.属性 [推荐]

##### 类方法和静态方法

类方法：针对类对象定义的方法，在类方法内部可以直接访问类属性或者调用其他的类方法

- 类方法需要用 修饰器：`@classmethod`标识
- 类方法的第一个参数是`cls`
  - 由哪一个类调用的方法，方法内部的`cls`就是哪一个类的引用
  - `cls`可用其他变量名替代
- 通过类名调用类方法，不需要传递`cls`参数

```python
class Tool(object):
    # 类属性，记录工具的数量
    count = 0

    @classmethod
    def show_tool_count(cls):
        print("Tool count==>%d" % cls.count)

    def __init__(self, name):
        self.name = name
        # 类属性的值+1
        Tool.count += 1


knife = Tool("knife")
fork = Tool("fork")

Tool.show_tool_count()  # Tool count==>2
```

静态方法

- 既不需要访问实例属性或者实例方法
- 也不需要访问类属性或者调用类方法
- `@staticmethod`标识静态方法
- 通过类名.方法名 调用静态方法

```python
class Tool(object):
    
    @staticmethod
    def work():
        print("Tool is working")

        
Tool.work()  # Tool is working
```

##### 实例方法、类方法、静态方法

实例方法：方法内部需要访问实例属性，能访问到类属性

类方法：方法内部只需要访问类属性

静态方法：方法内部不需要访问实例属性和类属性

##### 单例模式

单例模式
    让类创建的对象,在系统中只有为一个实例
    每一次执行类名()返回的对象,内存地址是相同的

`__new__`方法
    在内存中为对象分配空间
    返回对象的引用

重写`__new__`方法一定要 `return super().__new__(cls)`,否则Python的计时器得不到分配了空间的对象引用,就不会调用对象的初始化方法

> 注意:\_\_new\_\_是一个静态方法,在调用时需要主动传递cls参数

```python
class MusicPlayer(object):
    # 定义类属性记录单例对象引用
    instance = None
    # 定义类属性记录初始化是否调用
    init_flag = False

    def __new__(cls, *args, **kwargs):
        """"# 创建对象
        print("创建对象,分配内存空间")
        # 分配内存空间
        # 返回对象的引用
        return super().__new__(cls)
        """
        # 判断类属性是否是空对象
        if cls.instance is None:
            # 调用父类的方法,为第一个对象分配空间
            cls.instance = super().__new__(cls)
        # 返回类属性保存的对象引用
        return cls.instance

    def __init__(self):
        """
        # 初始化会被再次调用
        print("初始化音乐播放器")
        """
        # 初始化方法只调用一次
        if MusicPlayer.init_flag:
            return
        print("初始化音乐播放器")
        # 修改标记
        MusicPlayer.init_flag = True


player = MusicPlayer()
print(player)

newPlayer = MusicPlayer()
print(newPlayer)

# 两个对象的地址是一样的

```

#### 模块

###### 模块导入

import关键字导入模块，使python文件可供同一目录下的其他python文件使用

```python
# 1
import 模块名
调用：模块名.工具名

# 2
from 模块名 import 工具名	# 导入指定工具
# 如果两个模块存在同名的函数，后导入的模块函数会覆盖先导入的函数
# 取别名可以解决冲突

# 3
from 模块名 import *	# 导入模块下所有工具 
```

起别名 `import 模块名 as 别名`   

模块搜索顺序

搜索当前目录指定模块名文件，如果有就直接导入
如果没有，再搜索系统目录

> 在开发时，给文件起名，不要和系统的模块文件重名

Python中每一个模块都有一个内置属性`__new__`可以查看模块的完整路径

```python
import random

print(random)
# <module 'random' from 'D:\\Program Files\\Python37\\lib\\random.py'>
print(random.__file__)
# D:\Program Files\Python37\lib\random.py
```

> 注意：如果当前目录下，存在一个random.py的文件，程序就无法执行

###### \_\_main\_\_ 与 \_\_name\_\_ 

判断当前是否是主线程

```python
if __name__=="__main__":
	print("当前为主线程")
```

###### 搜索路径

```python
import sys
sys.path	#返回路径
sys.path.append(路径)		#追加需要导入的模块路径:本地路径
```

##### 包Package

- 包是一个包含多个模块的特殊的目录
- 目录下又有一个文件`__init__.py`
  - 在外界使用包中的模块，需要在`__init__.py`中对外界提供的模块列表
- 包名的命名方式和变量名一致，小写字母+`_`

###### 制作发布压缩包

- 创建`setup.py`文件

  ```python
  from distutils.core import setup
  
  setup(name="handle_message",  # 包名
        version="1.0",  # 版本
        description="描述信息",
        long_description="完整描述",
        author="author",  # 作者
        author_email="email",  # 作者邮箱
        url="www.xxx.com",  # 主页
        py_modules=[
            "handle_message.send_message",
            "handle_message.receive_message"])
  ```

- 构建模块

  ```shell
  python setup.py build
  ```

- 生成发布压缩包

  ```shell
  python setup.py sdist
  ```

#### 文件

###### 文件的读写

| 序号 | 函数/方法 | 说明                           |
| :--: | :-------: | ------------------------------ |
|  1   |   open    | 打开文件，并且返回文件操作对象 |
|  2   |   read    | 将文件内容读取到内存           |
|  3   |   write   | 将指定内容写入文件             |
|  4   |   close   | 关闭文件                       |

> read / write / close方法需要由文件对象来调用

调用格式 f = open(filename, mode,buffering)
filename:文件的路径
mode：模式  

- w 写
- r 读
- a 追加  

（b----二进制编码）不同模式可以混合
向文件中写入f.write(bytes)
文件读写完后要关闭  f.close()

`with open() as file `  会自动关闭文件，不需要手动执行.close()

```python
# 以二进制的方式写入图片到本地images文件夹中
def crawl_image(image_url, image_local_path):
    r = requests.get(image_url, stream=True)
    try:
        with open(image_local_path, "wb") as f:
            f.write(r.content)
            # f.close()
    except UnicodeDecodeError:  # 抛出异常
        print("save error")
```

文件指针：第一次打开文件时，通常文件指针会指向文件的开始位置，当执行了`read()`方法后，文件指针会移动到读取内容的末尾

###### 按行读取文件内容

readline()

```python
file = open("../README.txt")
while True:
    text = file.readline()
    # 判断是否读取到内容
    # if text is None:
    if not text:
        break
    print(text)

file.close()
```

###### 复制文件

读取源文件，写入新文件

Python2.x默认使用ASCII编码，Python3.x默认为UTF-8编码，txt编码默认为GBK编码

> 小文件的复制

```python
# 编码格式为UTF-8
origin = open("../README.txt", encoding="UTF-8")
new = open("../README_NEW.txt", "wb")

text = origin.read().encode("UTF-8")
new.write(text)

origin.close()
new.close()
```

> 大文件的复制

```python
# 编码格式为UTF-8
origin = open("../README.txt", encoding="UTF-8")
new = open("../README_NEW.txt", "wb")
# 读-写
while True:
    text = origin.readline().encode()
    # 判断是否读取到内容
    if not text:
        break
    new.write(text)

origin.close()
new.close()
```

###### 文件/目录的常用管理操作

> 导入os模块

文件操作

| 序号 | 方法名 | 说明       | 示例                           |
| ---- | ------ | ---------- | ------------------------------ |
| 01   | rename | 重命名文件 | os.rename(源文件名,目标文件名) |
| 02   | remove | 删除文件   | os.remove(文件名)              |

目录操作

| 序号 | 方法名     | 说明           | 示例                    |
| ---- | ---------- | -------------- | ----------------------- |
| 01   | listdir    | 目录列表       | os.listdit(目录名)      |
| 02   | mkdir      | 创建目录       | os.mkdir(目录名)        |
| 03   | rmdir      | 删除目录       | os.rmdir(目录名)        |
| 04   | getcwd     | 获取当前目录   | os.getcwd()             |
| 05   | chdir      | 修改工作目录   | os.chdir(目标目录)      |
| 06   | path.isdir | 判断是否是文件 | os.path.isdir(文件路径) |

> 提示：文件/目录操作都支持绝对路径和相对路径

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
