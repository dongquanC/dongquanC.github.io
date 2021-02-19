---
layout: post
title: scrapy + selenium 使用手法
categories: [程序语言]
tags: [python, 爬虫]
date: 2020-02-04
comments: false
---

# [scrapy](https://www.osgeo.cn/scrapy/intro/overview.html) + [selenium](https://python-selenium-zh.readthedocs.io/zh_CN/latest/) 使用手法

为了爬取百度图片，却发现百度图片网站改为了动态网页，基于scrapy不能实现，于是我尝试使用selenium + [PhantomJS](https://phantomjs.org/download.html) 实现。

## 下载器中间件

需要定义下载器中间件，对于selenium的内容可查询相关文档。
```
from selenium import webdriver
from selenium.common.exceptions import TimeoutException
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from scrapy.http import HtmlResponse
from logging import getLogger
import time

class SeleniumMiddleware():
    def __init__(self, timeout=None, service_args=[]):
        self.logger = getLogger(__name__)
        self.timeout = timeout
        self.browser = webdriver.PhantomJS(service_args=service_args)
        self.browser.set_window_size(1400, 700)
        self.browser.set_page_load_timeout(self.timeout)
        self.wait = WebDriverWait(self.browser, self.timeout)

    def __del__(self):
        self.browser.close()

    def process_request(self, request, spider):
        """
        用PhantomJS抓取页面
        :param request: Request对象
        :param spider: Spider对象
        :return: HtmlResponse
        """
        if 'image.baidu.com/search/flip' in request.url:
            self.logger.debug('image.baidu.com/search/flip PhantomJS is Starting')
            try:
                self.browser.get(request.url)
                time.sleep(3)
                self.wait.until(
                    EC.presence_of_element_located((By.CSS_SELECTOR, '.imglink')))

                return HtmlResponse(url=request.url, body=self.browser.page_source, request=request, encoding='utf-8', status=200)
            except TimeoutException:
                return HtmlResponse(url=request.url, status=500, request=request)
        elif 'image.baidu.com/search/detail' in request.url:
            self.logger.debug('image.baidu.com/search/detail PhantomJS is Starting')
            try:
                self.browser.get(request.url)
                time.sleep(3)
                self.wait.until(
                    EC.presence_of_element_located((By.CSS_SELECTOR, '#currentImg')))

                return HtmlResponse(url=request.url, body=self.browser.page_source, request=request, encoding='utf-8', status=200)
            except TimeoutException:
                return HtmlResponse(url=request.url, status=500, request=request)
            
    @classmethod
    def from_crawler(cls, crawler):
        return cls(timeout=crawler.settings.get('SELENIUM_TIMEOUT'),
                   service_args=crawler.settings.get('PHANTOMJS_SERVICE_ARGS'))

```

## setting

在setting.py中添加
```
ROBOTSTXT_OBEY = False
DOWNLOADER_MIDDLEWARES = {
    'getBaiduImg.middlewares.SeleniumMiddleware': 543,
    'getBaiduImg.middlewares.GetbaiduimgDownloaderMiddleware': 550,
}

#selenium与PhantomJS的配置
SELENIUM_TIMEOUT = 20
PHANTOMJS_SERVICE_ARGS = ['--disk-cache=true']
```

到这里selenium + PhantomJS 配置完成。


# 动态网页图片下载

## setting

```
ITEM_PIPELINES = {
   'getBaiduImg.pipelines.GetbaiduimgPipeline': 300,
   'getBaiduImg.pipelines.MyImagesPipeline': 100
}

#图片下载地址
IMAGES_STORE = 'C:\\Users\\20668\\Desktop\\img\\test2'
BOT_NAME = 'MyImagesPipeline'
```

## [pipelines](https://scrapy-chs.readthedocs.io/zh_CN/0.24/topics/downloader-middleware.html)

```
import scrapy
from scrapy.pipelines.images import ImagesPipeline
from scrapy.exceptions import DropItem

class GetbaiduimgPipeline(object):
    def process_item(self, item, spider):
        return item

class MyImagesPipeline(ImagesPipeline):

    def get_media_requests(self, item, info):
        for image_url in item['image_urls']:
            # print(image_url)
            yield scrapy.Request(image_url)

    def item_completed(self, results, item, info):
        image_paths = [x['path'] for ok, x in results if ok]
        if not image_paths:
            raise DropItem("Item contains no images")
        item['image_paths'] = image_paths
        # print(image_paths)
        return item
```
## item

```
class GetbaiduimgItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    image_urls = scrapy.Field()
    image_paths = scrapy.Field()
```

# 最后

百度图片网站的部分图片加载使用了缓存技术，无法爬去，即 **Cache-Control: max-age=0**。

附上scrapy 架构概览，解释下载器中间件会一个一个执行，数值越小越靠近下载器去，数值越大越靠近引擎。
![scrapy 架构概览](/assets/img/post/20200119%20scrapy%2Bselenium/scrapy_architecture.png)