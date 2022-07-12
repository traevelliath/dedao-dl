# dedao-dl

> 🦉 用 go 写的一个 《得到》 APP 课程下载工具，使用 cookie 登录后，可在终端查看已购买的课程，听书书架，电子书架，锦囊，推荐话题等

![GitHub go.mod Go version](https://img.shields.io/github/go-mod/go-version/yann0917/dedao-dl)

## 特别声明

仅供个人学习使用，请尊重版权，内容版权均为得到所有，请勿传播内容！！！

仅供个人学习使用，请尊重版权，内容版权均为得到所有，请勿传播内容！！！

仅供个人学习使用，请尊重版权，内容版权均为得到所有，请勿传播内容！！！

## 特性

* 可查看**购买**的课程，课程文章内容
* 可查看听书书架，电子书架列表
* 可查看已购买的锦囊
* 可查看知识城邦推荐话题精选内容
* 课程可生成PDF，文稿生成 Markdown 文档，也可生成 mp3 文件
* 每天听本书可下载音频
* 可切换登录账号

## 安装

### 安装依赖

* chromedp
  > 生成 PDF 需要借助 [chromedp](https://github.com/chromedp/chromedp), 该功能必须安装 [Google-Chrome](https://www.google.cn/intl/zh-CN/chrome/) 浏览器
* ffmpeg
  > 音频需要借助 [ffmpeg](https://ffmpeg.org/) 合成

### 使用 `go get` 安装

使用如下命令安装：

`go get -u github.com/yann0917/dedao-dl`

### 使用 Docker 运行

> 为了加快 build 速度，`alpine` 镜像源已修改为阿里镜像。

如果不想在本地安装 `ffmpeg` 和 `chromedp` 则提供了 `docker` 环境，参考以下命令构建并使用容器执行相关命令。

```bash
# build
docker build https://github.com/yann0917/dedao-dl.git#main -t dedao

# 登录
docker run -v `pwd`/config.json:/app/config.json -it --rm dedao login -c "CookieString placeholder"

docker run -v `pwd`/config.json:/app/config.json -it --rm dedao cat
# 查看课程
docker run -v `pwd`/config.json:/app/config.json -it --rm dedao course

# 下载课程
docker run -v `pwd`/output:/app/output -v `pwd`/config.json:/app/config.json -it --rm dedao dl xxx
# 下载每天听本书
docker run -v `pwd`/output:/app/output -v `pwd`/config.json:/app/config.json -it --rm dedao dlo xxx

```

#### 删除 `docker images` 中为 `none` 的镜像

```bash
docker ps -a | grep "Exited" | awk '{print $1 }'|xargs docker stop
docker ps -a | grep "Exited" | awk '{print $1 }'|xargs docker rm
docker images|grep none|awk '{print $3 }'|xargs docker rmi
```

## 使用方法

* 电脑端登录 [得到](https://www.dedao.cn)，以便生成 cookie ☆☆☆☆☆

`dedao-dl -h` 可查看帮助说明，每个命令都有 `-h` 参数可查看该命令的用法

```bash
Usage:
  dedao-dl [command]

Available Commands:
  ace         获取我的锦囊
  article     获取文章详情
  cat         获取课程分类
  course      获取我购买过课程
  dl          下载已购买课程, 并转换成 PDF & 音频 & markdown
  dlo         下载每天听本书音频
  ebook       获取我的电子书架
  help        Help about any command
  login       登录得到 pc 端 https://www.dedao.cn
  odob        获取我的听书书架
  su          切换登录账号
  topic       获取推荐话题列表
  users       查看登录过的用户列表
  who         查看当前登录的用户
```

`dedao-dl login` 登录，不带任何参数的情况下，默认使用 [`go-rod/rod`](https://github.com/go-rod/rod) 从浏览器获取cookie. 如果无法自动获取 cookie，则使用 `dedao-dl login -c "xxxxxxxx"` 登录

`dedao-dl cat` 获取课程分类

```text
+---+----------+------+----------+
| # |   名称   |  统计 |  分类标签  |
+---+----------+------+----------+
| 0 | 全部     | 1696 | all      |
| 1 | 课程     |   64 | bauhinia |
| 2 | 听书书架  | 1407 | odob     |
| 3 | 电子书架  |  210 | ebook    |
| 4 | 锦囊      |   15 | compass  |
+---+----------+------+----------+
```

`dedao-dl course` 获取我购买过课程

```text
+----+-----+-------------------------+--------------------------------+---------------------+--------+----------+
| #  | ID  |          课程名称        |                作者             |      购买日期       |  价格  | 学习进度 |
+----+-----+-------------------------+--------------------------------+---------------------+--------+----------+
|  0 |  51 | 张潇雨·个人投资课          | 张潇雨·对冲基金管理人            | 2020-08-17 09:59:56 | 199.00 |      100 |
|  1 | 560 | 吴军·硅谷来信³（年度日更） | 吴军·计算机科学家                 | 2020-11-05 09:28:23 | 299.00 |       14 |
|  2 | 571 | 年度得到·何帆中国经济报告  | 何帆·著名经济学者                 | 2020-12-10 07:22:28 |  99.00 |       36 |
|  3 |  21 | 古典·超级个体              | 古典·资深生涯规划师             | 2019-09-23 17:11:02 | 199.00 |       84 |
|  4 |  79 | 老喻的人生算法课           | 老喻·思考者                     | 2019-09-23 17:13:51 |  99.00 |      100 |
|  5 |  84 | 给忙碌者的女性健康课       | 常青·协和医学院博士               | 2020-10-27 21:30:55 |  19.90 |      100 |
|  6 |  16 | 陈海贤·自我发展心理学      | 陈海贤·心理学博士                 | 2019-09-23 17:13:46 |  99.00 |      100 |
|  7 | 544 | 听书番外篇                | 阿狮·每天听本书负责人             | 2020-08-18 15:43:44 |   0.00 |      100 |
|  8 | 530 | 跟李松蔚学心理咨询         | 李松蔚·心理咨询师                 | 2020-07-01 11:01:44 |  99.00 |      100 |
|  9 | 484 | 王立铭·抑郁症医学课        | 王立铭·浙江大学生命科学研究院教授    | 2020-10-27 13:15:36 |  39.90 |      100 |
| 10 |  82 | 陈海贤·亲密关系30讲        | 陈海贤·心理学博士                 | 2019-11-05 00:02:21 |  99.00 |      100 |
+----+-----+-------------------------+---------------------------------+---------------------+--------+----------+
```

`dedao-dl course -i xxx` 查看课程信息

```text
专栏名称：张潇雨·个人投资课
专栏作者：张潇雨·对冲基金管理人
更新进度：60/60
课程亮点：1. 这是一门适用于普通投资者的课程。即使你没有专业技巧，没有额外的时间精力，也可以通过课程找到适合自己的投资策略与工具，建立自己的投资体系。

2. 这门课程能帮你重建正确的投资常识，从源头上杜绝投资失误。个人投资本来是一条宽敞平坦的康庄大道。只是有太多错误的岔路需要避免。课程的前14讲都在给你指出岔路，带你回到正途。

3. 投资的难点永远在于实操。课程中有16讲实操内容，帮你解决不知道投资产品怎么选、选什么的问题，以及当极端情况出现时你该怎么办。

4. 张潇雨老师在投资银行、私募基金、对冲基金、家族办公室都曾任职，可以说管理过各种各样的钱。所以，他能告诉你为什么很多看似高深的投资方法恰恰不能用于个人投资。

5. 这一次，知识真的就是财富。

+---+------+----------------------------+------+---------------------+--------------+
| # |  ID  |            章节            | 讲数 |      更新时间       | 是否更新完成 |
+---+------+----------------------------+------+---------------------+--------------+
| 0 | 1040 | 导论(3讲)                  |    3 | 2019-05-05 23:52:31 | ✔            |
| 1 | 1041 | 模块一：市场规律(6讲)      |    6 | 2019-06-02 00:26:29 | ✔            |
| 2 | 1042 | 模块二：投资工具(6讲)      |    6 | 2019-06-02 00:26:36 | ✔            |
| 3 | 1043 | 模块三：自我局限(6讲)      |    6 | 2019-06-02 00:26:43 | ✔            |
| 4 | 1044 | 模块四：投资组合构建(9讲)  |    9 | 2019-06-02 00:26:50 | ✔            |
| 5 | 1503 | 模块五：投资实战(16讲)     |   16 | 2020-09-14 14:46:30 | ✔            |
| 6 | 1045 | 结语(1讲)                  |    1 | 2020-09-14 15:07:06 | ✔            |
| 7 | 1046 | 附录(2讲)                  |    2 | 2020-09-14 15:07:00 | ✔            |
| 8 | 1502 | 加餐丨货币的基础逻辑(11讲) |   11 | 2020-09-14 14:48:57 | ✔            |
+---+------+----------------------------+------+---------------------+--------------+
```

`dedao-dl article -i xxx` 查看课程文章信息

```markdown
+----+-------+----------------------------------------------------------+---------------------+----------+
| #  |  ID   |                         课程名称                         |      更新时间       | 是否阅读 |
+----+-------+----------------------------------------------------------+---------------------+----------+
|  0 | 86033 | 发刊词丨这一次，知识就是财富                             | 2019-05-05 23:44:19 | ✔        |
|  1 | 86037 | 01丨普通投资者的优势                                     | 2019-05-05 23:44:33 | ✔        |
|  2 | 86038 | 02丨普通投资者的劣势                                     | 2019-05-05 23:44:57 | ✔        |
|  3 | 86040 | 03丨多元分散：房子还会不会是表现最好的资产？             | 2019-05-05 23:45:24 | ✔        |
|  4 | 86051 | 04丨择时陷阱：“低买高卖”可以实现么？                     | 2019-05-07 00:00:00 | ✔        |
|  5 | 86074 | 05丨宏观迷信：自下而上的投资逻辑                         | 2019-05-08 00:00:01 | ✔        |
|  6 | 86080 | 06丨风险度量：怎样真正地理解风险和亏损？                 | 2019-05-09 00:00:02 | ✔        |
|  7 | 86081 | 07丨海外配置：人人都可以买下全球市场                     | 2019-05-10 00:00:00 | ✔        |
|  8 | 86082 | 08丨第一模块问答：该何时卖出一只股票？                   | 2019-05-11 00:00:01 | ✔        |
|  9 | 86083 | 09丨指数基金：买到伟大公司的最好机会                     | 2019-05-12 00:00:00 | ✔        |
| 10 | 86086 | 10丨安全边际：好公司不等于好股票                         | 2019-05-13 00:00:00 | ✔        |
| 11 | 86087 | 11丨抄底哲学：市场的不理性与买入时机                     | 2019-05-14 00:00:00 | ✔        |
| 12 | 86181 | 12丨主动管理：不要盲信专业光环                           | 2019-05-15 00:00:01 | ✔        |
| 13 | 86193 | 13丨交易成本：快速提高真实收益的最佳方法                 | 2019-05-16 00:00:02 | ✔        |
| 14 | 86215 | 14丨第二模块问答：不谈技术，最重要的投资心法是什么？     | 2019-05-17 00:00:00 | ✔        |
| 15 | 86230 | 15丨过度自信：投资赚到钱一定是好事么？                   | 2019-05-18 00:00:03 | ✔        |
| 16 | 86248 | 16丨反向复利：你能接受“慢慢变富”吗？                     | 2019-05-19 00:00:01 | ✔        |
| 17 | 86251 | 17丨利益错位：如何对待别人的投资建议？                   | 2019-05-20 00:00:00 | ✔        |
| 18 | 86271 | 18丨场外因素：决定成败的非技术原因                       | 2019-05-21 00:00:01 | ✔        |
| 19 | 86290 | 19丨五大原则：投资很简单，但并不容易                     | 2019-05-22 00:00:03 | ✔        |
| 20 | 86307 | 20丨第三模块问答：我学了一个金融学理论，可以用来投资吗？ | 2019-05-23 00:00:03 | ✔        |
| 21 | 86312 | 21丨收益与回撤：搭建投资组合的关键要素                   | 2019-05-24 00:00:01 | ✔        |
| 22 | 86333 | 22丨大类资产：投资组合中常见工具的分析                   | 2019-05-25 00:00:01 | ✔        |
| 23 | 86349 | 23丨长期投资策略1：永久组合                              | 2019-05-26 00:00:02 | ✔        |
| 24 | 86362 | 24丨长期投资策略2：全天候组合                            | 2019-05-27 00:00:03 | ✔        |
| 25 | 86374 | 25丨长期投资策略3：斯文森组合                            | 2019-05-28 00:00:01 | ✔        |
| 26 | 86394 | 26丨股票投资策略进阶：因子投资                           | 2019-05-29 00:00:02 | ✔        |
| 27 | 86411 | 27丨定投指数基金1：投资中国股市能赚钱么？                | 2019-05-30 00:00:04 | ✔        |
| 28 | 86420 | 28丨定投指数基金2：指数基金的价值投资法                  | 2019-05-31 00:00:01 | ✔        |
| 29 | 86436 | 29丨第四模块问答：盈利了之后，我们该做什么？             | 2019-06-01 00:00:02 | ✔        |
| 30 | 91162 | 工具1丨再平衡：市场波动时，怎样调整投资组合？            | 2020-09-14 14:46:45 | ✔        |
| 31 | 91171 | 工具2丨“四笔钱”：以财富管理视角做长期投资                | 2020-09-14 14:46:45 | ✔        |
| 32 | 91173 | 工具3丨趋势投资：股灾与大跌有可能躲过吗？                | 2020-09-14 14:46:45 | ✔        |
| 33 | 91183 | 工具4丨量化对冲：“旱涝保收”的基金产品选择                | 2020-09-14 14:46:45 | ✔        |
| 34 | 91192 | 工具5丨“固收+”：理财产品净值化转型的稳健选择             | 2020-09-14 14:46:45 | ✔        |
| 35 | 91198 | 工具6丨网格交易法：市场小幅波动时如何取得收益？          | 2020-09-14 14:46:45 | ✔        |
| 36 | 91202 | 策略1丨风格轮动：怎样做基金层面的价值投资者？            | 2020-09-14 14:46:45 | ✔        |
| 37 | 91226 | 策略2丨止盈与止损：个股买卖的时机选择                    | 2020-09-14 14:46:45 | ✔        |
| 38 | 91243 | 策略3丨信仰持仓：我们最可能长期持有什么样的股票？        | 2020-09-14 14:46:45 | ✔        |
| 39 | 91244 | 策略4丨非对称收益：怎样建立持股的“风投”仓位？            | 2020-09-14 14:46:45 | ✔        |
| 40 | 91245 | 策略5丨另类货币：如何配置投资组合里的“核心资产”？        | 2020-09-14 14:46:45 | ✔        |
| 41 | 91246 | 策略6丨指数增强：如何通过“抄作业”提升收益？              | 2020-09-14 14:46:45 | ✔        |
| 42 | 91247 | 策略7丨情景预案：买股票前要问的四个问题                  | 2020-09-14 14:46:45 | ✔        |
| 43 | 91306 | 进阶1丨风险敞口：怎样识别规避家庭多重风险？              | 2020-09-14 14:46:45 | ✔        |
| 44 | 91311 | 进阶2丨利益攸关：如何选择投资顾问与第三方产品？          | 2020-09-14 14:46:45 | ✔        |
| 45 | 91329 | 进阶3丨清单革命：如何构建自己的投资清单体系？            | 2020-09-14 14:46:45 | ✔        |
| 46 | 86444 | 结束语丨和常识、时间与概率站在一起                       | 2020-09-14 16:58:38 | ✔        |
| 47 | 86459 | 附录1丨张潇雨的最全投资书单                              | 2020-09-14 15:07:07 | ❌       |
| 48 | 86474 | 附录2丨常用投资工具与资源指南                            | 2020-09-14 15:07:06 | ❌       |
| 49 | 91058 | 01丨为什么没有货币交换，交易也可以发生？                 | 2020-09-14 14:46:30 | ✔        |
| 50 | 91065 | 02丨钱就是债务，债务就是钱                               | 2020-09-14 14:46:30 | ✔        |
| 51 | 91069 | 03丨美联储到底是一个什么机构？                           | 2020-09-17 12:48:34 | ✔        |
| 52 | 91085 | 04丨货币滥发给世界带来了什么影响？                       | 2020-09-14 14:46:30 | ✔        |
| 53 | 91090 | 05丨美国是如何收割世界财富的？                           | 2020-09-14 14:46:30 | ✔        |
| 54 | 91094 | 06丨汇率的变动机制是什么？                               | 2020-09-14 14:46:30 | ✔        |
| 55 | 91102 | 07丨央行的难处是什么？                                   | 2020-09-14 14:46:30 | ✔        |
| 56 | 91104 | 08丨美元为什么又叫美金？                                 | 2020-09-14 14:46:30 | ✔        |
| 57 | 91122 | 09丨为什么说美元霸权难以为继？                           | 2020-09-14 14:46:30 | ✔        |
| 58 | 91128 | 10丨怎样打破美元霸权？                                   | 2020-09-14 14:46:30 | ✔        |
| 59 | 91130 | 附录丨货币的四条基础法则+书单                            | 2020-09-14 14:46:30 | ✔        |
+----+-------+----------------------------------------------------------+---------------------+----------+
```

`dedao-dl dl 123 -t 1` 下载课程ID 123 的所有课程, -t 下载格式, 1:mp3, 2:PDF文档, 3:markdown文档 (default 1)

注意：生成 PDF 的时候，操作过于频繁会触发 `496 NoCertificate` , 因此每次生成一次PDF sleep 0~5秒, 尽管如此，还是有极大可能触发操作频繁图形验证。

## References

* [geektime-dl](https://github.com/mmzou/geektime-dl)
* [annie](https://github.com/iawia002/annie)
* [m3u8](https://github.com/oopsguy/m3u8)

## Stargazers over time

[![Stargazers over time](https://starchart.cc/yann0917/dedao-dl.svg)](https://starchart.cc/yann0917/dedao-dl)

## License

[MIT](./LICENSE) © yann0917

## Support

[![jetbrains](https://s1.ax1x.com/2020/03/26/G9uQoR.png)](https://www.jetbrains.com/?from=dedao-dl)

---
