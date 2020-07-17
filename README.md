# DM_scrapy
#### 介绍
- 结合python和scrapy框架，从豆瓣电影top250中，爬取相应信息存储在test.csv文件中，并且实现了scrapyhub的部署。
- [scrapyhub部署公布](https://app.scrapinghub.com/datasets/q3QXh8j5AQp)（需先登录）
#### 项目名称
- 豆瓣电影scrapy框架爬虫
#### 数据信息
- [csv数据](https://gitee.com/LI_ZzHeng/DM_scrapy/blob/master/test.csv)
- 页数：10
- 维度：describe、movie_name、evaluate、introduce、serial_name、star
#### 数据加值宣言
- 本项目产出以电影名称、电影描述、评论人数、电影基本信息、电影排名及电影评分做维度挖掘的关于豆瓣电影top250的数据，以解决一些关于电影分类的问题。
#### 数据最小可用产品(MVP的数据加值)
##### 数据的维度
- describe：电影的描述
- movie_name：电影的名称。
- evaluate：电影在豆瓣中的评论人数，数据类型str。
- introduce：电影的简介，包括有年份/国家/类型/，数据格式都是以/为分隔符。
- serial_name：电影的排行名称，数据类型str。
- star：电影的评分，数据类型str。
##### 解决的问题
- 利用introduce维度做一个电影类型关键词的分类，再利用python的数据分析模块来对关键词出现的数量进行一个统计，探究电影类型关键词出现的次数（例：剧情类：195、奇幻：36），从而可以分析出观众对不同类型电影的喜爱程度。
- 利用introduce维度提取出电影的出产国和电影的类型关键词，再利用python的数据分析来进行一个分析排序，探究不同的国家再拍摄电影类型上存在着什么样的差异。（例：美国：犯罪类70，中国：犯罪类10）
- 对describe维度的电影描述做一个的LDA主题模型分析，将文本中关键词进行矢量化，分析主题对应文本的机率分布值。
- 利用star维度（电影评分）结合introduce维度中的出产国、电影类型、年份进行一个关联分析，探究不同出产国、电影类型、年份的一个评分情况。
- 利用evaluate维度（评论人数）进行一个排序的分析，进行一个电影的热度排行。

#### 挖掘Query参数
- next_link：页数

#### 思路方法及具体执行

1. 在cmd搭建scrapy环境。

```python
pip install scrapy
```

2. 在jupyter输入以下代码自动化搭建出一个scrapy的初始框架。

```python
! scrapy startproject douban-master
```
3. 输入以下代码定义域名信息，并且自动化在spiders文件夹中创建自己的爬虫项目。

```python
! scrapy genspider SpiderName "movie.douban.com"
```
4. 在scrapy框架中，设置好爬虫名、域名与及入口url，然后在框架parse函数部分写入爬虫的解析方法，利用xpath对网页的信息进行抓取，中间运用到split的方法对抓取的数据进行清洗，处理字符串，去除换行与空格，在翻页操作上，利用xpath提取最后一页的页码，在使用for循环来加载下一页的跳转，最终将抓取的所有数据 yield到管道里。
- 入口url设置

```python
start_urls = ['https://movie.douban.com/top250']
```
- 运用到的数据清洗

```python
content_s = "".join(i_content.split())
```

5. 然后在items.py进行一个单元的输出设置。

```python
    #序号
    serial_name = scrapy.Field()
    #电影名
    movie_name = scrapy.Field()
    #电影介绍
    introduce = scrapy.Field()
    #电影星级
    star = scrapy.Field()
    #电影评论数
    evaluate = scrapy.Field()
    #电影的描述
    describe = scrapy.Field()
``` 

#### 心得总结及感谢
- xapth是一个强大的路径语言，主要分为绝对路径、相对路径和标签属性三种定位方式，除了js动态内容，基本可以抓取所有页面的基本信息，结合scrapy框架，使得数据的挖掘更为的结构化与及自动化，在爬虫的过程中，如果遇到403或者是418等报错的时候，是我们比较常遇到被反爬的情况，总结了一下对策，分为一下两种：伪造hearder请求头和设置爬虫时间的间隔，使得我们的行为更加的像人从而避免反爬，以上是我学习了爬虫后一个比较简短的心得，最后还需要感谢我的两位导师所给予的教育与帮助。