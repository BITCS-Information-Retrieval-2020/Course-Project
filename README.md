# 论文综合搜索引擎

## 项目介绍

搭建一个学术论文的综合搜索引擎，用户可以检索到一篇论文的综合信息，不仅有pdf文件，还有oral视频，数据集，源代码等多模态信息。

输入：

```
文字
```

输出：

```
根据相关性由大到小排序的论文列表，其中的每一项包含： 
- 论文的数据(标题、作者、摘要、出版机构及年份等)(必须有)
- 论文的pdf以及出版网页链接
- 论文的相关代码
- 论文的数据集
- 论文的oral视频
```

样例页面：[https://crossminds.ai/video/5fb82261890833803bc7e7f8/](https://crossminds.ai/video/5fb82261890833803bc7e7f8/)

## 模块划分

### 1. 爬虫模块

#### 推荐爬取的站点

- CrossMinds：[https://crossminds.ai](https://crossminds.ai)（含视频、介绍文字、代码链接等）
- ACL Anthology：[https://www.aclweb.org/anthology/](https://www.aclweb.org/anthology/)（含PDF、视频、数据集链接等）
- Papers With Code: [https://paperswithcode.com](https://paperswithcode.com) （含PDF、代码链接等）
- PaperWeekly：[https://www.paperweekly.site](https://www.paperweekly.site)（含介绍文字，PDF，代码链接等）
- DBLP：[https://dblp.uni-trier.de](https://dblp.uni-trier.de)
- Semantic Scholar：[https://www.semanticscholar.org](https://www.semanticscholar.org)
- IEEE Xplore：[https://ieeexplore.ieee.org/Xplore/home.jsp](https://ieeexplore.ieee.org/Xplore/home.jsp)
- 各大会议期刊的网站

#### 要求

- [CrossMinds](https://crossminds.ai)必选，要保证爬到的论文中有一定比例包含oral视频，爬取到的必须是视频的文件，记录下url地址。
- 除CrossMinds外再至少选择一个站点爬取，爬取的站点越多，包含的字段越多，爬到的数据越多，最终得分越高。
- 爬到的数据必须存储到MongoDB（[https://www.mongodb.com](https://www.mongodb.com)）中，供后续模块访问使用。字段必须定义清晰。
- 论文标题，作者等文本字段直接存在数据库里，视频，PDF等二进制文件存在磁盘上，数据库里保存文件的路径。
- 爬虫可以是简单地一次性把数据爬完，也可以是增量式爬取，时时更新，可以是单线程，也可以是多线程，或使用线程池。总之，爬虫的性能越强，越健壮，得分越多。
- 最后需要提供爬到数据的统计信息。
- 推荐使用Python Scrapy爬虫库：[https://scrapy.org](https://scrapy.org)
- **模块接口要求**：爬虫模块最终需要交给检索模块一个**MongoDB数据库**和**磁盘文件**。


### 2. 检索模块

#### 要求

- 从MongoDB中读取爬虫爬到的数据，建立索引，实现综合检索。
- 可以自己实现搜索算法，也可以使用已有的搜索引擎工具，比如Elasticsearch（[https://www.elastic.co](https://www.elastic.co)）
- 如果自己实现算法，功能可以不用特别强，但需要说明原理和使用方法。如果使用已有的搜索引擎工具，需要理解工具的使用方法和运行原理，尽量多地探索工具提供的功能。
- 即可以实现全文检索，也可以实现指定字段的检索，可以指定从某一个或若干字段检索。比如从标题和摘要检索。
- 需要把视频中的语音识别为文字，并记录每段文字在视频中的时间。如果用户检索视频内容，要能返回视频，同时定位到视频中该文字出现的位置。
- 把论文PDF转为文本，供检索使用。
- 不需要等爬虫模块全部爬完之后才实现检索模块，可以使用少量样例数据，实现检索功能。各模块的开发是并行推进的。
- 可以与爬虫模块协商，共同确定MongoDB数据库中的字段名称和类型，最终实现两模块联动。
- **模块接口要求**：最终提供给系统展示模块**一个可以用pip安装的Python包**，安装之后，在代码中import包中的函数和类，即可实现检索模块提供的所有功能。

### 3. 系统展示模块

#### 要求

- 设计并实现一个学术论文的搜索引擎网站前后端，至少包含这三种页面：首页，搜索结果列表页，论文的详细信息页。可以参考[CrossMinds](https://crossminds.ai)。
- 首页不仅要有搜索框，还要可以供用户选择要检索的字段（可以同时选择多个字段，比如标题、作者、摘要、全部等等）。用户在搜索框输入文字时，可以实时提示补全信息。（Ajax技术）
- 搜索结果列表页将搜索到的论文按关联度排序展示。
- 论文详细信息页要能播放与论文相关的视频。
- 实现网站架构的前后端分离，数据部分和展示部分分离(MVC)，推荐使用[vue.js](https://cn.vuejs.org/)和[ElementUI](https://element.eleme.cn/#/zh-CN)。
- 推荐使用Python Django（[https://www.djangoproject.com](https://www.djangoproject.com)）库来实现网站。
- 不需要等检索模块全部完成之后才实现展示模块，和检索模块定义好接口即可进行开发。各模块的开发是并行推进的。
- **模块接口要求**：检索功能通过调用**检索模块提供的Python包**完成，任务开始之前需要和检索模块商量好Python包提供的接口定义。

## 共同要求

- 团队协作：每个小组在Github Organization上创建代码仓库，所有的代码都放在代码仓库中，但是不要上传数据等大文件(通过`.gitignore`文件实现)。
- 对外交流：认真撰写README文件，要求内容完整，条理清晰，该文件作为评分的重要依据。
- 代码风格：验收时会用Python的Flake8（[https://flake8.pycqa.org/en/latest/](https://flake8.pycqa.org/en/latest/)）库检查每个小组的代码是否规范，通过检查即可获得代码风格分数，不通过则不会获得。


  命令行flake8
  ```sh
  pip install flake8
  flake8 --max-line-length 127 --ignore=F401,W503 ./
  ```

  vscode配置
  ```
  {
    "python.linting.enabled": true,
    "python.linting.flake8Enabled": true,
    "python.linting.flake8Args": [
        "--max-line-length=127",
        "--ignore=F401, W503"
    ]
  }
  ```

## 组队要求

- 全班78名同学，分为12个队，每队6~7个人，每个队伍选择一个模块，每个模块4个队伍
- 通过填写腾讯文档报名(在微信群里公布链接)
- 三个不同模块的队伍自由组合成一个大队，三个小队的队长负责模块之间接口的协商，最后大队需要提交一个各个模块组成的可以展示的系统。
- 组队完成之后，每位同学点下方对应模块的链接，根据页面引导自动创建/加入队伍，并创建Github仓库。所有的仓库都会建在[BITCS-Information-Retrieval-2020](https://github.com/BITCS-Information-Retrieval-2020)这个Github Organization账号下。
- 爬虫模块：[https://classroom.github.com/g/4nAXa0-U](https://classroom.github.com/g/4nAXa0-U)
- 检索模块：[https://classroom.github.com/g/9wUWvhFd](https://classroom.github.com/g/9wUWvhFd)
- 展示模块：[https://classroom.github.com/g/LwQvXP16](https://classroom.github.com/g/LwQvXP16)



## 验收

|   模块   | 大组编号 |           组名           |  组长  | 组员                                               |
| :------: | :------: | :----------------------: | :----: | :------------------------------------------------- |
| 爬虫模块 |    1     | Low-levelMasterDoCrawler | 雷云霖 | 马放，李家伟，蔡健，吴为伦，魏博，宦紫仪           |
| 检索模块 |    1     | Low-levelMasterDoSearch  | 朱婧婧 | 熊婧雯，赫宇欣，姚翛潇，程文浩，李易为，孙昊       |
| 展示模块 |    1     | Low-levelMasterDoWebShow | 惠子杨 | 蒋少杰，王志鹏，田效宇，李坤，邱思思               |
| 爬虫模块 |    2     |       HandsomeGuys       | 方致远 | 杨毅哲，刘啸尘，张骁，郑和奇，周啸峰，刘梦歌       |
| 爬虫模块 |    2     |      ICanOnlyCrawl       | 何亮丽 | 徐伊玲，谭小焱，张峄天，詹亘泽，张悉伦             |
| 检索模块 |    2     |     ratTailCollagen1     | 聂宇翔 | 王昊，兰天，程赛华，孙凡淑，公培元，孔祥宜，闻子涵 |
| 展示模块 |    2     |        day2night         | 刘雨程 | 付卓新，李四贝，邬慧燕，党迎旭，陈凯悦，刘晓晨     |
| 爬虫模块 |    3     |      daoerxianqiao       | 姜景虎 | 郑洪超，寇桓锦，杨俊，朱牛牛，张鑫，周长智         |
| 检索模块 |    3     |           TDXL           | 王洪飞 | 高佳蕊，冯昭凯，刘君，刘旭冬，王大为，纪校锋       |
| 展示模块 |    3     |        helloworld        |  苗壮  | 孙逸鲲，魏雅倩，胡英帅，翟钰坤，罗晓青，郑慧娴     |
| 展示模块 |    3     |        positive          | 刘千会 | 李延铭，余佳奇，王红麟，牛泳，张登凯，刘琦         |

**时间**：2021年**1月17日**（周日），上午8:00\~12:00，下午15:00\~17:00

| 大组编号 |              组长              |  验收时间   |
| :------: | :----------------------------: | :---------: |
|    1     |     雷云霖、朱婧婧、惠子杨     | 8:00-10:00  |
|    2     | 方致远、何亮丽、聂宇翔、刘雨程 | 10:00-12:00 |
|    3     |  姜景虎、王洪飞、苗壮、刘千会  | 15:00-17:00 |

**地点**：暂定中教1003会议室，如有变动，会在微信群通知

- 会议室有投影，需要投影展示，投影仪为HDMI接口，请各组自备转接头

**验收流程**：

- 验收以大组为单位，首先大组总体展示(10min展示+5min提问)，然后组内各小组分小组展示(10min展示+5min提问)
- 大组展示内容包括：整体效果、功能特色、接口定义情况、小组间协作情况
- 小组展示内容包括：模块整体效果、模块功能特色、系统架构、代码实现、团队分工
- 展示形式不做任何限制，以讲清楚以上两条为目的，PPT可根据展示需要选择，不会因为做或不做PPT而加分或扣分

**文件提交：**

- 代码部分在验收开始前应当全部上传到小组的GitHub仓库，代码应当结构清晰，必须包含README文件
- README文件应该是现场展示内容的图文描述版，此外还要讲述清楚代码目录结构，代码必要说明，部署过程，启动运行流程，依赖的第三方库的目录及版本等，总之，达到路人看到这个文件就能启动这个模块的效果

- 数据部分(比如爬虫爬下来的数据)按照清晰的结构进行存储，验收当天以大组为单位组织为一个文件夹拷到助教的移动硬盘中，文件夹中也应当包含一个说明文档



此文档的最终解释权归助教[@xhd](https://github.com/DaDaMrX)及[@xcx](https://github.com/fihxc)所没有→_→

