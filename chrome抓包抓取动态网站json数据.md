---
title: chrome抓包抓取动态网站json数据
tags:
  - python
  - json
  - chrome抓包
abbrlink: aea01abd
date: 2020-04-03 12:00:00
---

#### 步骤

- 用chrome的开发者工具获取动态网址的json数据的url  
	- 在chrome中右键检查，选择network选项
	- 重新加载页面，找到需要爬取的数据
	- 查看request url即为真实请求的url
	- url即为json数据
- 使用json库下的loads函数将数据的类型转换成字典类型

#### 实例代码
举例：抓取百度搜索关键字--电影 的网站
```python
import re
import json
import requests


def catch(page):
	# 每一个网页的url中的 pn 是递增的--8
    pn = page * 8
    url = "https://sp0.baidu.com/8aQDcjqpAAV3otqbppnN2DJv/api.php?resource_id=28286&from_mid=1&&format=json&ie=utf-8&oe=utf-8&query=%E7%94%B5%E5%BD%B1&sort_key=16&sort_type=1&stat0=&stat1=&stat2=&stat3=&pn=" + str(
        pn) + "&rn=8&cb=jQuery110204230762934017789_1575516372377&_=1575516372379"
    res = requests.get(url)
    json_str_re = re.compile("{.*}")
    json_str = json_str_re.search(res.text).group()
    movie_dict = json.loads(json_str)
    # 根据json—handle工具，自动排列查看所获取信息
    for movie in movie_dict["data"][0]["result"]:
        print(movie["ename"])


if __name__ == '__main__':
    catch(1)	# 查询第一页内容

```

#### 相关链接
[json-handle插件](http://jsonhandle.sinaapp.com/)
