---
layout: page
title: SCRAPY PROJECT MODEL 2
tags: [Scrapy, Python]
excerpt: Scrapy Model 2.About [cgdict.com].
date: 2020-05-06
comments: true
project: true
---
# PROJECT ADDRESS
[scrapy project(cgdict.com)](https://github.com/dongquanC/scrapy-project/tree/master/english%20scrapy/english)  

# 亮点
无，普普通通的项目。  

# COMMAND
```shell
scrapy startproject projectname
```
```shell
scrapy genspider sitename siteurl
```
```shell
scrapy crawl spidername
scrapy crawl spidername -o jsonname.json
```

# 贴个SPIDER部分代码

start_requests.

```python
def start_requests(self):
    url = 'http://www.cgdict.com/index.php?app=cigen&ac=list&page='
    for i in range(8):
        yield self.make_requests_from_url(url+str(i+1))    
```