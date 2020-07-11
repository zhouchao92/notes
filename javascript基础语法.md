---
title: javascrpit基础语法
tags:
  - javascript
categories:
  - notes
  - web
abbrlink: 95725add
date: 2020-04-03 12:00:00
---

### js的声明
1. 在head标签中使用script标签进行js代码域的声明

   ```js
   <script type="text/javascript">
   	alert("This is my first js.")；//测试
   </script>
   ```

   作用：声明js代码域
   特点：js的代码只会作用于当前网页  

2. 在head标签中使用script标签引入外部声明好的js文件

   ```js
   <script type="text/javascript" src="相对路径" charset="utf-8"></script>
   ```

   作用：引入外部声明好的js文件
   特点：实现js代码的重复使用，避免代码的冗余
   注意：因为js在HTML文档中是一门单独的语言，可以声明在文档中的任意位置	 一般情况下声明在head标签中

<!--more-->

### js的变量
- js的所有变量声明只有var关键字
  var 变量名=初始值；
  注意： 

  - js的变量名是严格区分大小写的
  - js的字符串可以使用双引号也可以使用单引号
  - js中可以声明同名变量，不会报错，后面的声明会将前面的声明覆盖 

- js中的数据类型
  数据类型判断关键字：`typeof`
  数据类型：

  - number:数字类型 

  - string：字符串类型 

  - boolean：布尔型

  - object：对象

  - null：空对象赋值，主要是和undefined进行区分

  - undefined：变量声明不赋值，默认值是undefined

    注意：`var d;  alert(d);  alert(typeof c);`  在js中尽量给声明的变量赋初值

- js变量强制转换

  - 使用Number函数，将其他数据类型转换成数值类型，转换失败返回NaN（Not a Number）

  | 数据类型 | 原始值     | 转换后            |
  | -------- | ---------- | ----------------- |
  | string   | 数字字符   | 对应的nunber数字  |
  | string   | 非数字字符 | NaN（number类型） |
  | boolean  | true       | 1                 |
  | boolean  | false      | 0                 |
  | object   | 有具体的值 | 一串数字          |
  | object   | null       | 0                 |

- Boolean()函数，将其他数据类型转换成布尔类型，有值返回true，无值返回false

  | 数据类型         | 原始值       | 转换后的值 |
  | ---------------- | ------------ | ---------- |
  | number           | 正数或者负数 | true       |
  | number           | 0            | false      |
  | string           | 非空字符串   | true       |
  | string           | 空字符串""   | false      |
  | object           | 具体的对象   | true       |
  | object           | null         | false      |
  | 声明不赋值的变量 |              | false      |

- js运算符

  算术运算符:加+    减-    乘*    除/    求余%
  number类型和number类型的
  number类型和boolean类型(true--1    false--0)
  number类型和string类型
  string和string类型的数字
  string类型和boolean类型

在算术运算中如果两边的数据类型并不是number，会使用Number()函数强制转换

注意：在字符串中"+"符号代表的是字符串的连接符,不会参与运算 

- 逻辑运算符: 非 ！    与 & &&      或 | || 
- 关系运算符：！=    >=    <=
- 自增运算符：++    --    -=                 // 运算效率高（位运算）   

- 特殊关系运算符：

  ```javascript
  等值运算符==：先判断类型，类型一致则直接比较，类型不一致，则先使用Number（）进行强转后在进行比较
  eg:
  null==undefined            //返回值为true
  
  等同运算符：===	先判断类型，类型一致则再比较内容，内容一致则返回true，内容不一致则返回false
  eg:
  null===undefined         //返回值为false
  ```

### js的逻辑结构与循环结构
js的逻辑结构：
- if结构：
  - 单分支结构 if()
  - 双分支结构 if()  else()
  - 多分支结构 if() else if() ...else()
- switch选择结构：`switch（）{  case：break；}`    

注意：判断的变量可以是number类型也可以是string类型，但不要混用

js的循环结构：

- for()循环
- while()循环
- do{}while()循环

### js的数组
- 数组的声明

  ```javascript
  var array=new Array();           //声明空数组对象
  var array=new Array(length);     //声明数组长度指定的数组
  var array=[元素...];             //直接声明数组（常用）    
  ```

  注意：js中的数组声明不用指定长度，js数组的长度是不固定的，由元素的个数而定     

- 数组的赋值和取值
  数组可以存储任意类型的数据

  - 组名[角标]=值；  //角标可以是任意的正整数和零

  组的取出

  - 组名[角标]；       //返回当前角标所对应存储的值如果角标不存在，返回undefined

- 数组的length属性
  - 作用：
    - 数组名.length            //返回当前数组长度
    - 数组名.length=值       //动态设置数组长度
  - 注意：
    - length>原有长度，则使用空进行填充
    - length<原有长度，则从后面进行截取，最后的数据会被

- 数组的遍历
  - 普通for循环
  - for-in
  
  ```javascript
  for(var i in array){
      alert(i);   //获取的是角标
      alert(array[i]);  //获取的是元素
  }
  ```
#### js数组的常用操作
- 数组的合并：

  `var a=array.concat(数组);`

- 数组指定间隔符转换字符串：

  `var b=array.join("-");`

- 数组移除最后一个元素并返回元素：

  `var c=array.pop();`

- 数组的追加,返回数组的新长度：

  `var d=array.push(元素);`   //最佳的元素可以是一个数组，但是作为一个角标值存在

- 数组移除第一个元素并返回该元素：

  `var e=array.shift();`

- 数组在开始位置插入指定元素并返回数组的新长度：

  `var f=array.unshift("");`

- 数组删除指定位置元素并返回移除的元素

  `var g=array.splice(start,deletecount,"");` 

### js的函数：
作用：功能代码块的封顶，减少代码的冗余

- 函数的声明
	- 方式一：
	  
	  ```javascript
	  function 函数名(参数){
	      函数体
	  }
	  调用函数；函数名（实际参数）；
	  ```
	  
	- 方式二：
	  `var 函数名=new Function（"形参1"，“形参2"..."函数执行体"）；`
	  注意：此声明表明在js函数中是作为对象存在的
    
  - 方式三：
    
    ```javascript
    var 变量名=function（形参...）{
        函数体；
    }
    ```
  
- 函数的参数
js的函数调用时，形参可以不赋值，不会报错
js的函数调用时，形参赋值可以不全部赋值，不会报错，但实参会依次赋值
- 函数的返回值
在js中如果有返回值则返回返回值，无返回值则返回undefined
注意：js的代码声明区域和执行区域是一起的，都是在js代码的代码域中

- 函数的执行符
  在js中函数的执行符值()
  没有小括号则函数名其实就是一个变量
  加上小括号则函数会被执行
- 函数作为实参传递
  在js中函数是可以作为实参进行传递的

```javascript
eg:
function testobj(a){
	alert(a());
}
var testParam=function(){
	alert("函数参数");
}
testobj(testParam());
```

### js的类和对象

####  js的类：
- 类的声明     
  ```javascript
   function 类名（形参...）{
   	this.属性名=形参；
   	...
   	this.属性名=function(){}
   }
  ```
  
- 类的使用
        对象名=new 类名（实参...）;
        
    
        注意：js中类的内容是对象的公共部分，每个对象还可以自定义地进行扩充
    
- 类的"继承"     prototype关键字
 通过prototype关键字实现了不同对象之间的数据共享
作用:实现某个类的所有子对象的方法区对象的共享，节省内存

   prototype写在类里面，调用时要加上prototype
   prototype写在类外面，调用时不加prototype
   
     ```javascript
   //声明
   function Person(name,age){
       this.name=name;
       this.age=age;
       this.fav="Sing";
       this.prototype=new User();    
   }
   function User(){}
   //调用
   //或者Person.prototype=new User();
   var p1=new Person("盲侠",20);
   User.prototype.testU=function(){alert("abcd");}
   //此处p1.prototype.testU();
   p1.prototype.testU();    
     ```
   

#### 自定义对象

对象的作用：用来存储整体数据
原因：不能预知一个对象的所有属性，创建一个临时对象来定义属性存储数据，来保证数据的完整性
应用：Ajax
使用：

- 创建自定义对象

  ```javascript
  var 对象名=new Object();
  对象名.属性名=属性值;
  ...
  
  var 对象名={}；
  对象名.属性名=属性值;
  ...
  ```
- 一般用来存储数值，不会再自定义对象中存储函数对象    
- js中的对象属性和内容是可以自定义的扩充到的，不是依赖于类的声明的，类只是对象公开部分的一种声明，是为了节省代码的冗余


### 常用对象和方法
#### string对象
字符串大小写的转换

- str.toUpperCase();  将字符串转换为大写
- str.toLowerCase();  将字符串转换为小写

字符串的切割

- var str=string.split("字符")；按照指定的字符切割字符串，返回数组

字符串的截取

- str.substr(start,end);  从指定的开始位置截取指定长度的子字符串
- str.substring(start,end); 从指定的开始位置和指定的结束位置截取子字符串，含头不含尾

查找子字符串第一次出现的角标

- indexOf("字符");   返回指定子字符串第一次出现的角标，没有则返回-1

#### Date对象
获取当前年份

- d.getYear();      返回1990年开始距今的年份数
- d.getFullYear();  返回当前年份
- d.getMonth()+1;   返回当前月份的角标值，需要+1
- d.getDate();      返回的是当前的日期数
- d.getHours();      返回当前时间的小时数l
- d.getMinutes();   返回当前时间的分钟数
- d.getSeconds();      返回当前时间的秒数

#### Math对象
- 创建随机数字：`Math.random();` 返回0-1之间的随机数，不包括0和1
  
- 向下取整：`Math.floor();`
  
- 向上取整：`Math.ceil();`
  
- 四舍五入：`Math.round();`
  
- 数值比较    

    - `Math.min(数据...);` 获取最小值
    - `Math.max(数据...);`  获取最大值

#### Global对象
该对象从不直接使用并且不能new，直接写方法名调用即可
- 使用eval将字符转换为可执行的js代码
   eval();
- 使用isNaN判断是否值为NaN
    isNaN();
 - 获取字符中的浮点数
     parseFloat("字符串");

### js的事件机制
#### 事件机制
- 解释；当行为动作满足了一定的条件后，会触发某类事务的执行
作用：主要是结合js的函数来使用
- 内容：
   - 单双击事件
       单击：onclick
       双击：ondblclick
	- 鼠标事件
       悬停：onmouseover
       移动：onmousemove
       移出：onmouseout
   - 键盘事件
       松开：onkeyup
       按下：onkeydown
   - 焦点事件
       获取：onfocus
       失去：onblur
   - 网页加载事件
       加载：onload
       一般作为网页资源加载，页面加载成功

注意：

- js中添加事件的第一种方式：在HTML上直接使用事件属性进行添加，属性值为所监听执行的函数
- js中的事件只有在当前HTML元素上有效
  一个HTML元素可以添加多个不同的事件
  一个事件可以监听触发多个函数执行，不同函数要使用；间隔 

#### 用途
- 给合适的html标签添加合适的事件
   onchange-----select下拉框
   onload-------body标签
   单双击--------用户进行点击动作的HTML元素
   鼠标事件------用户进行鼠标移动操作的
   键盘事件------用户进行键盘操作的HTML元素
   
- 给HTML元素添加多个事件，注意事件之间的冲突
   eg：单双击
   当事件的触发事件包含相同部分的时候，会产生事件之间的冲突
   
- 事件的阻断
   当事件监听的函数的的返回值返回给事件时：
   false：则会阻断当前事件所在的HTML标签的功能
   true：则继续执行当前事件所在的HTML标签的功能
   
- 超链接调用js函数

   ```html
   <a href="javascript:函数名()">描述</a>
   ```


### window对象
BOM浏览器对象模型：是规范浏览器对js语言的支持（js掉用浏览器本身的功能）
BOM的具体实现是window对象

#### window对象

1. window对象不用new，直接进行使用，类似Math的使用方式，widow关键字可以省略

2. 框体方法
        
        - 警告框     alert();      //提示一个警告信息，无返回值
        - 确认框     confirm();      //提示用户选择一项操作（确定/取消） 返回true或false
        - 提示框    prompt();      //提示用户某个信息的录入    点击确定返回当前用户录入的数据，默认返回空字符串；点击取消，返回null
    
3. 定时和间隔执行方法
	- 定时
	setTimeout(函数对象，时间(毫秒));  //指定的时间后执行指定的函数
	- 间隔执行
		setInterval(函数对象，时间(毫秒)); //每间隔指定时间执行指定的函数
	- 停止
        - clearTimeout(定时器id);                //停止当前的定时器 ,返回当前定时器id
        - clearInterval(间隔器id);                 //停止当前指定的间隔器，返回当前间隔器id
   
4. 子窗口方法

      ```javascript
      window.open();   //打开新页面
      参数(子页面资源（相对路径），打开方式，配置)       
      window.close();  //关闭子页面
      
      window.open('page.html','newwindow','height=100px,width=100px,top=0px,left=0px,toolbar=no,menubar=no,scrollbar=no,resizeble=no,location=no,status=no');
      ```

      注意：close（）只能关闭open（）打开的子页面！二者配套使用
      
5. 子页面调用父页面的函数
window.opener.父类函数名();
#### window对象常用属性

- 地址栏属性
window.location.href="新的资源路径（相对路径/url）";        //跳转资源
window.location.reload();                                 //重新加载页面资源

- 历史记录属性
window.history.forward();    //页面资源前进，历史记录的前进
window.history.back();       //页面资源后退，历史记录的后退
window.history.go(index);    //跳转到指定的历史记录资源
注意：window.history.go(0)；相当于刷新页面
- 屏幕属性
var width=window.screen.width;    //获取屏幕的宽度分辨率
var height=window.screen.height;  //获取屏幕的高度分辨率

- 浏览器配置属性
window.navigator.userAgent();    //获取用户的浏览器版本信息...

- 主体面板属性(document)
### document对象
#### document对象的概念
浏览器对外提供的支持js的用来操作HTML文档的一个对象，此对象存的HTML文档的所有信息（隶属于window对象）

#### 使用document
##### 获取HTML元素对象
###### 直接获取方式
|属性|方法  |
|--|--|
|  id | (window.)document.getElementById(); |
|name属性值	| (window.)document.getElementsByName();          //返回数组	|
|	标签名| (window.)doucument.getElementsByTagName();      //返回数组	|
|	class属性值|	(window.)document.getElementsByClassName();     //返回数组|

###### 间接获取方式
- 父子关系
获取父级元素对象：var showdiv=document.getElementById();
  获取所有的子元素对象数组：var childs=showdiv.childNodes;

- 子父关系
获取子元素对象
  var inp=document.getElementByid();
  var div=inp.parentNode;

- 兄弟关系
var inp=document.getElementById();
var pre=inp.previousSibling;
var next=inp.nextSibling;

##### 操作HTML的元素属性
- 获取元素对象
  直接or间接
- 操作元素属性
  
   - 元素对象名.属性名                              //获取固有属性
   
    - 元素对象名.getAttribute("属性名");     //获取自定义属性修改
    - 元素对象名.属性值=属性值                            //修改固有属性
    - 元素对象名.setAttribute("属性名","属性值");    //修改自定义属性         
   
   注意：尽量不要修改元素对象的id，name属性
   使用自定义方式获取固有属性内容，value的额值获取的是默认值，不能获取到实时的用户数据

##### 操作HTML元素对象的内容和样式
- 元素内容
	- 获取元素对象
	- 获取元素内容
    对象名.innerHTML;                          //返回当前元素对象的所有内容，包括HTML标签
    对象名.innerText;                        //返回当前元素对象的文本内容，不包括HTML标签
	- 修改元素内容
    对象名.innerHTML="";                        //会将原有内容覆盖，且HTML标签会被执行
    对象名.innerHTML=对象名.innerHTML+"";        //不会将原有内容覆盖，且HTML标签会被执行
    对象名.innerText="";                       //会将原有内容覆盖，但HTML标签不会被执行，会作为普通文本显示
- 元素样式(实际相当于操作元素属性)
	
	- style属性
    - 添加/修改元素样式
	       元素对象名.style.样式名="样式值";     //样式名为css样式名去-且驼峰命名原则
         
    - 删除
	
         元素对象名.style.样式名="";
         注意：以上操作，操作的是HTML的style属性声明中的样式，而不是其他css代码域中的样式    
    
  - class属性
    元素对象名.className="新的值"   添加/修改/删除类选择器样式           
##### 操作HTML的文档结构
增加节点
删除节点

- 使用innerHTML(适用下拉框内容填充)
div.innerHTML=div.innerHTML+'内容';     //增加节点
div.innerHTML="";                      //删除所有子节点
父节点.removeChild(子节点对象)          //删除指定子节点

-  使用document
var obj=document.createElement("标签名");
元素对象名.appendChild(obj);        //添加
元素对象名.removeChild(obj);        //删除

##### document操作form元素
- 获取form表单对象   (form 标签 id="frm" name="frm")
    使用id： var fm=document.getElementById("fm");
    使用name属性: var frm=document.frm;
- 获取form表单的所有子元素标签
    表单对象.elements
- form表单的常用方法
    表单对象.submit();        //提交
    表单对象.reset();            //重置
- form的属性操作
    表单对象名.action="新的值"    //动态的改变数据的提交路径
    表单对象名.method="新的值"    //动态的改变数据的提交方式
- 表单元素的通用属性
    只读模式        readonly="readonly"        //不可以更改，但是数据可以提交
    关闭模式        disabled="disabled"        //不可进行任何操作，数据不会提交
- 操作表单元素

    - 多选框、单选框    

      被选中状态在js中checked属性值为true，未选中为false

    - 下拉框

      被选择的option对象在js中selected属性值为true，未选中为false（select对象.options返回一个option数组）

#####    document对象实现form表单校验(验证码、用户名)
- 创建验证码
  
    - 创建随机四位数字
        `var code=Math.floor(Math.random()*9000+1000);`
        
    - 将数字放在span中
      
        `var span=document.getElementById("span");`
        `span.innerHTML=code;`
    
- 验证(用户名   id="uname")
     封装校验...相同的保留，不同的传参
     
     1. 获取用户名信息
         `var uname=document.getElementById("uname").value;`
         
     2. 创建检验规则-----正则表达式
     
         ```javascript
         var reg=/^[\u4e00-\u9fa5]{2,4}$/;      //用户名：2-4个汉字
         /^[a-z]\w{5,7}$/;                   //密码：字母打头后5-7位数字
         /^1[3,4,5,6,7,8]\d{9}$/;         //手机号检验：1开头，第二位为[3,4,5,6,7,8]，共11位
         ```
3. 获取span对象
   
    `var span=document.getElementById("unameSpan");`
    
4. 开始校验
   
    ```javascript
    if(uname==""||uname==null){
        //输出校验结果
        span.innerHTML="用户名不能为空";
        span.style.color="red";
    }else if(reg.test(uname)){
        //输出校验结果
        span.innerHTML="用户名可用";
        span.style.color="green";
    }else{
        //输出校验结果
        span.innerHTML="用户名不符合规范";
        span.style.color="red";
    }
    ```
    
5. 提交判断：form  onchanged属性          

#####    document操作表格
######        删除行
```javascript
function delRow(btn){                                
    var table=document.getElementById("");  //获取表格table对象
    var tr=btn.parentNode.parentNode;       //获取要删除的行号
    table.deleteRow(tr.rowIndex);           //tr.rowIndex返回行对象的角标
}
```
###### 修改行功能
```javascript
function updateRow(btn){
    var tr=btn.parentNode.parentNode;   //获取表格table对象
    var cell=tr.cell[];					//获取行对象
    if(isNaN(cell.innerHTML)){			//判断cell.innerHTML的值是否为数字(修复修改功能的bug)
        //修改行内容
        cell.innerHTML="<input type='text'"+cell.innerHTML+"onblur='updateInp(this)'"
    }
}
function uodateInp(inp){
    var cell=inp.parentNode;
    cell.innerHTML=inp.value;
}
```
###### 选择删除指定行功能
```javascript
var table=document.getElementById("");    //获取表格对象
var checks=document.getElementsByName("");//获取要删除的行号
//开始删除
for(var i=0;i<checks.length;i++){
    if(check[i].checked){
        table.deleteRow(i);
        i--;    //数组的length值动态变化
    }
}
```
###### 添加行
```javascript
var table=document.getElementById("");    //获取表格对象
var tr=table.insertRow(1);                //添加行
var cell=tr.insertCell(0);                //添加单元格
cell.innerHTML="";                        //单元格内容
```

###### 复制行
```javascript
var table=document.getElementById("");            //获取表格对象
var checks=document.getElementsByName("");        //获取选择行对象
for(var i=1;i<checks.length;i++){                 //i=1;保留表头
    if(checks[i].checked){
        var tr=table.insertRow(table.rows.length);
        tr.innerHTML=table.rows[i].innerHTML;     //复制行
    }
}
```

###### 全选：用一个radio操作多个单选框(name相同)
```javascript
function chekedAll(){
    var check=document.getElementById("");
    var chks=document.getElementsByName("");
    if(check.checked){
        for(var i=0;i<chks.length;i++){
            chks[i].checked=true;
        }
    }else{
        for(var i=0;i<chks.length;i++){
            chks[i].checked=false;
        }
    }
}
```

​    
