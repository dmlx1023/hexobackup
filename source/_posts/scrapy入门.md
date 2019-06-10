---
title: scrapy入门
date: 2019-05-27 20:15:17
tags:
---

# scrapy入门



##  安装

scrapy安装比较麻烦，这里使用anaconda安装非常简单

```shell
conda create scrapydemo #创建一个虚拟环境
activate scrapydemo #激活环境
conda install scrapy #安装，输入y继续安装，等待结束
```

常用*conda*命令

```shell
conda list #查看安装的包
conda create -n env_name python=3 #创建特定版本的环境
conda env remove -n env_name #删除指定环境
source activate my_env/activate my_env #进入环境 linux/windwos
source deactivate/deactivate #离开环境 linux/windwos
conda env export > environment.yaml #导出当前环境 
pip freeze > requirements.txt  #导出当前环境，非conda
conda env create -f environment.yaml #根据文件创建环境
pip install -r requirements.txt #根据文件创建环境，非conda
conda create -n BBB --clone AAA #根据AAA环境创建BBB环境
```

## scrapy入门

1.创建工程

```
scrapy startproject taobao #创建工程
scrapy genspider example example.com #创建一个爬虫
#启动
scrapy crawl spider_name #命令行启动
# 在spider文件内启动
if __name__ == '__main__':
    from scrapy import cmdline
    args = "scrapy crawl spider_name".split()
    cmdline.execute(args)

```

2.这里使用pycharm开发，打开taobao工程，指定环境为前面conda创建的scrapydemo环境，这个环境安装好了scrapy。

pipeline间传输数据

> ```python
> yield scrapy.Request(item['url'], headers=headers, meta={'item': item})
> ```

使用下面的在request接收

> ```python
> folder = request.meta['item']['folder']
> ```

3.使用ImagesPipeline下载图片

settings.py

```python
ITEM_PIPELINES = {
   'spirderxxoo.pipelines.ImagesrenamePipeline': 300,
}
IMAGES_STORE = 'D:\ImagesRename'
```

pipelines.py

```python
class ImagesrenamePipeline(ImagesPipeline):
    def file_path(self, request, response=None, info=None):
        str = request.url
        fileName = str.split('/')[-3:len(str)]
        dir = 'F:\spider_down\image'
        folder = request.meta['item']['folder']
        pathprefx = dir + os.sep + folder
        path = pathprefx + os.sep + ''.join(fileName)
        return path

    def get_media_requests(self, item, info):
        headers = {'Accept': 'image/png, image/svg+xml, image/*; q=0.8, */*; q=0.5',
                   'Accept-Encoding': 'gzip, deflate, br',
                   'Accept-Language': 'zh-Hans-CN, zh-Hans; q=0.5',
                   'Cache-Control': 'max-age=0',
                   'Host': 'i.meizitu.net',
                   'Referer': 'https://www.mzitu.com',
                   # 'Referer': 'https://www.mzitu.com/180015/6',
                   'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/18.17763'}

        yield scrapy.Request(item['url'], headers=headers, meta={'item': item})
```

如何使用FilesPipeline下载文件：

```python
ITEM_PIPELINES = {
   'scrapy.pipelines.files.FilesPipeline': 300, #参数越小优先级越高
}
FILES_STROE = 'D:\ImagesRename'
```

item字段

``` python
file_urls = scrapy.Field()  # 指定文件下载的连接
    files = scrapy.Field()      #文件下载完成后会往里面写相关的信息
```

下载完成后文件名为hash值，可以通过重写def file_path(self, request, response=None, info=None)，来重命名文件。

4. middlewares.py 重写request以及response
通过from_crawler来传递参数以及初始化数据
 ```python
  class Middleware(object):
 
    def __init__(self, headers):
        self.headers = headers
 
    @classmethod
    def from_crawler(cls, crawler):
        settings = crawler.settings
        headers= settings.getint('HEADERS')
        return cls(maxdepth, headers)
 ```

通过重写 `process_response`**(**request**,** *response***,** *spider***)** 来修改请求。