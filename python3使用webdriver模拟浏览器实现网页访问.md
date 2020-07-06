---
title: python3使用webdriver模拟浏览器实现网页访问
tags:
  - webdriver
categories:
  - python
abbrlink: '86288129'
date: 2020-04-03 12:00:00
---

#### webdriver

###### 配置
- 进入网址[http://npm.taobao.org/mirrors/chromedriver/](http://npm.taobao.org/mirrors/chromedriver/)下载与安装的google chrome版本相对应的chrome driver
- 将下载的chromedriver.exe添加到python项目文件夹中

#### 模式
通过对webdriver进行设置可以实现静默运行和通过窗口运行

<!--more-->

##### 窗口运行

运行python文件后会打开一个chrome窗口对网页进行访问
###### 具体代码

```python
from selenium import webdriver

# webdriver配置，静默运行
driver = webdriver.Chrome()  
driver.viewportSize = {'width': 1920, 'height': 900}	# 分辨率
driver.maximize_window()	# 可使窗口最大化
url=''
driver.get(url)	# 对网页进行访问
print(driver.page_source)	# 打印网页源码
```
##### 静默运行
运行python文件后不会打开chrome窗口，以静默的方式对网页进行访问
###### 具体代码
```python
from selenium import webdriver

# webdriver配置，静默运行
option = webdriver.ChromeOptions()
option.add_argument('headless')  # 设置option
driver = webdriver.Chrome(options=option)  # 调用带参数的谷歌浏览器
driver.viewportSize = {'width': 1920, 'height': 900}	# 分辨率
url=''
driver.get(url)
print(driver.page_source)	# 打印网页源码
```

#### 相关方法
可以通过网站[webdriver](https://www.w3.org/TR/webdriver/)进行相关文档的查询
###### 元素查找
```python

element=driver.find_element_by_xpath()	# 通过绝对路径对网页的某一元素进行查找

element=driver.find_element_by_class_name()	# 类名查找

element=driver.find_element_by_id()		# id查找

element=driver.find_element_by_name()	# name查找
    
```
###### 截屏
```python

element.screenshot("文件名.png")	 # 将元素内容截屏保存成png格式，静默运行方式下可以执行，在窗口模式下运行时	

```

###### 输入内容
```python

element.send_keys('')	# 对元素进行填写内容，可用于输入框  

```

###### 网页刷新
```python

driver.refresh()	# 浏览器刷新网页

```

##### 网页信息

```python

driver.title() 			# 获取网页的标题

driver.page_source	# 获取网页源码

driver.get_cookies()	# 获取浏览器cookie

driver.current_url		# 获取当前url

```


