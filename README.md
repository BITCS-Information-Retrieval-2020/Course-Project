# 全字段论文搜索引擎

## 项目介绍

搭建一个全字段的论文搜索引擎，用户可以输入关于论文的任何信息来检索想要的论文，包括论文题目，作者，摘要，关键词，标签，正文等。系统返回的每篇论文包含关于这篇论文的全字段信息，包括题目，作者，初版单位，初版年份，摘要，标签，网页链接，PDF原文，作者的参会视频，源代码链接，数据集链接，介绍文字等。



## 模块划分

### 1. 爬虫模块

#### 推荐爬取的站点

- CrossMinds：[https://crossminds.ai](https://crossminds.ai)（含视频，介绍文字等）
- ACL Anthology：[https://www.aclweb.org/anthology/](https://www.aclweb.org/anthology/)（含PDF、视频、数据集链接等）
- Papers With Code: [https://paperswithcode.com](https://paperswithcode.com) （含PDF、代码链接等）
- PaperWeekly：[https://www.paperweekly.site](https://www.paperweekly.site)（含介绍文字，PDF，代码链接等）
- DBLP：[https://dblp.uni-trier.de](https://dblp.uni-trier.de)
- Semantic Scholar：[(https://www.semanticscholar.org](https://www.semanticscholar.org)
- IEEE Xplore：[https://ieeexplore.ieee.org/Xplore/home.jsp](https://ieeexplore.ieee.org/Xplore/home.jsp)
- 各大会议期刊的网站

#### 要求

- [CrossMinds](https://crossminds.ai)必选，要保证爬到的论文中有一定比例包含视频。视频信息包含原视频的url（可以嵌入网页播放），能下载视频源文件的下载源文件。
- 除CrossMinds外再至少选择一个站点爬取，爬取的站点越多，包含的字段越多，爬到的数据越多，最终得分越多。
- 对爬到的数据要做数据清洗，保证数据的质量。
- 爬到的数据存储到MongoDB（[https://www.mongodb.com](https://www.mongodb.com)）中，供后续模块访问使用。字段须定义清晰。
- 爬虫的性能越强，越健壮，得分越多，可以是简单地一次性把数据爬完，也可以是增量式爬取，时时更新，可以是单线程，也可以是多线程，或使用线程池。
- 最后提供爬到数据的统计信息。
- 推荐使用Python Scrapy爬虫库：[https://scrapy.org](https://scrapy.org)


### 2. 检索模块

#### 要求

- 从MongoDB中读取爬虫爬到的数据，建立索引，实现全字段检索。
- 可以自己实现搜索算法，也可以使用已有的搜索引擎工具，比如Elasticsearch（[https://www.elastic.co](https://www.elastic.co)）
- 自己实现的算法，功能可以不用特别强，但需要说明原理和使用方法。使用已有的搜索引擎工具，需要理解工具的使用方法和运行原理，尽量多地探索工具提供的功能。
- 实现指定字段的检索，可以指定从某一个或若干字段检索。比如从标题和摘要检索。
- 最终提供一个可以用pip安装的Python包，安装之后，在代码中import包中的函数和类，即可实现检索功能。
- 不需要等爬虫模块全部爬完之后才实现检索模块，可以使用少量样例数据，实现检索功能。各模块并行推进。
- 可以与爬虫模块协商，共同确定MongoDB数据库中的字段名称和类型，最终实现两模块联动。



### 3. 系统展示模块

#### 要求

- 设计并实现一个搜索引擎网站，至少包含这三种页面：首页，搜索结果列表页，一篇论文等详细信息页。
- 首页不仅要有搜索框，还要可以供用户选择要检索等字段（可以同时选择多个字段）。
- 用户在搜索框输入文字时，可以实时提示补全信息。（Ajax技术）
- 论文详细信息页要能播放与论文相关的视频。
- 实现网站的前端页面、后段逻辑以及前后端的交互。后端通过import检索模块来使用检索功能。
- 推荐使用Python Django（[https://www.djangoproject.com](https://www.djangoproject.com)）库来实现网站。



## 共同要求

- 每个小组在Github Organization上创建代码仓库，所有的代码都放在代码仓库中，但是不要上传数据等大文件。
- 认真撰写README文件，该文件作为评分的重要依据。
- 验收时会用Python的Flake8（[https://flake8.pycqa.org/en/latest/](https://flake8.pycqa.org/en/latest/)）库检查每个小组的代码是否规范，通过检查即可获得代码风格分数，不通过则不会获得。


  命令行flake8
  ```sh
  pip install flake8
  flake8 --max-line-length 127 --ignore=F401,W503 .
  ```
  
  vscode配置
  ```
  {
    "python.linting.enabled": true,
    "python.linting.flake8Enabled": true
  }
  ```
