---
layout: page
title: SCRAPY PROJECT MODEL 3
tags: [Scrapy, Python]
excerpt: Scrapy Model 3.About [lz13.cn].
date: 2020-05-10
comments: true
project: true
---

# 思路

Spider->Item->Pipelines->Setting.

[This Project.](https://github.com/dongquanC/scrapy-project/tree/master/lz13%20scrapy)

# Spider

```
注意点[1]
allowed_domains = ['www.lz13.cn'] # 这里注意不要有'/'之类的接在后面。
start_urls = ['https://www.lz13.cn/']

注意点[2]
# 在构造Request时，meta参数可以作为参数传递的载体，meta内容并不会提交到请求中。
# callback 为回调函数。
yield scrapy.Request(url = href,meta={'txt':txt[0]}, callback = self.href_parse)
```

# Item

没啥说的，直接如下：
```
txt = scrapy.Field()
content = scrapy.Field()
```

# Pipelines

```
注意点[3]
# 写入json，不会写的话直接copy下面的内容。
def __init__(self):
    print('Lz13Pipeline start.')
    self.filename=open("xx.json","wb")
def process_item(self, item, spider):
    jsontext=json.dumps(dict(item),ensure_ascii=False) + ",\n"
    self.filename.write(jsontext.encode("utf-8"))
    return item
def close_spider(self,spider):
    self.filename.close()
```

# Setting

```
ITEM_PIPELINES = {
    'lz13.pipelines.Lz13Pipeline': 300,
}
```

# 结语

学习scrapy去懂scrapy的架构概览可以节省大量的学习时间。
