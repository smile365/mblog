---
title:  如何开发设计一套百度/微信/淘宝指数产品
heading:  
date: 2020-02-27T06:14:24.971Z
categories: ["code"]
tags: ["百度指数","微信指数"]
description: 
---

公司有一款舆情分析的产品，里面有一个功能是**智能分析**。功能有点类似百度指数。前两天大boss（俗称ceo）在会上反馈公司的智能分析功能出不来结果，关键时刻掉链子。

做为一个无所不能的技术，怎么能让人说技术不行呢。

因为这个产品不是我们部门开发的，所以下班时间抽空去抓了一下包，分析了一遍上面的功能，发现有很多地方可以改进，以下分五个部分来说明：

 - 一、智能分析现状
 - 二、查询不出结果或慢的主要原因
 - 三、“竟品”分析和借鉴
 - 四、是要“全”还是要“快”
 - 五、改进方案的效果评估
 
**一、现状分析**

任何互联网产品本质其实是数据输入、数据处理、数据输出的过程。目前数据输入包括两部分，一个是用户的查询条件，另一个是公司采集的资讯数据。数据处理的过程大体为，系统按照用户设定的条件对资讯进行统计，最后输出统计结果。

智能分析的功能包括：态势分析、印象分析、爆点分析、评价分析、用户分析、类别分析、节点分析，以下是对这几个功能点的数据梳理。


态势分析
输入：关键词、时间范围、数据源、评价（积极、中级、消极）
输出：不同来源在时间范围内每隔1小时的资讯数量折线图。不同来源的数据占比。


印象分析
输入：关键词、时间范围、数据源
输出：词云，与之相关的词，在时间段内的占比。

爆点分析：
输入：关键词、时间范围、数据源、评价（积极、中级、消极）
输出：印象（词云）在时间范围内的1小时折线图

评价分析
输入：关键词、时间范围、数据源
输出：时间段内评价数量，每一小时的评价数量。


用户分析
输入：关键词、时间范围、数据源、评价
输出：地区分布比例，男女比例

类别分析
输入：关键词、时间范围、数据源、评价
输出：类别占比（社会时政、健康医疗、投资理财、健康养生、娱乐明星）

节点分析
输入：关键词、评价
输出：新闻列表


以上功能点用户可操作的数据项有：关键词、时间、数据源（27个）、评价（3个）、类别（5个）。


**二、查询不出结果或慢的主要原因**

体验的过程中我猜测，此系统可能是批处理模式：用户输入条件，系统根据条件取出数据，计算机统计后输出结果。

举个例子，假设用户在“态势分析”下搜索“疫情”，时间范围选择24小时，其余默认。服务器的处理步骤为：

 1. 从所有数据中（假设90亿条）筛选出最近24小时的数据（假设1亿条）
 2. 从这1亿条中找出提到“疫情”的数据（假设34万条）
 3. 统计34万条数据不同来源的资讯数量
 4. 统计34万条数据每个小时的资讯数量

每一个步骤都是对一条数据进行比对，符合的筛选出来，不符合的略过。假设比对1亿条数据需要1秒，那么步骤1里比对90亿条需要90秒。1条资讯有很多关键词，假设1条新闻平均3000个字，有300个关键词。1亿条资讯需要比对300亿个词，步骤2需要300秒。忽略步骤3和4，仅仅1和2就需要将近400秒。

如何提升类似批处理数据的影响速度呢？

常见的方法是把热门的查询条件提前算好，增加缓存等等。比如用户A搜来“疫情”，把A的结果缓存起来，当用户B按相同条件搜“疫情”的时候，直接把A的计算结果返回给B，这样省去了再次计算的时间。凭借小伙伴们的聪明才智，肯定还能想出很多优化的方法。但这类方法总归“治标不治本”，无论如何优化，只要遇到服务器没计算过的查询条件，用户就得等。




**三、“竟品”分析和借鉴**

一年前我体验智能分析这个功能发现，它与百度指数/微信指数等产品极其相似。设计上应该是一款指数类产品。

推荐设计此产品的技术小伙伴，去深度体验第三方指数产品，比如百度指数/微信指数/微指数（微博）/好搜指数/google趋势/淘宝指数/头条指数。重点考虑两点：一、为什么不免费对用户提供任意关键词的查询；二、统计的数据为什么不提供小时级别的时间精度。

这一块的分析描述起来太多，暂时不做过多介绍，一般深度体验几天后基本会有一个清晰的认识。


**四、是要全还是要快**

先得到用户的查询条件，再去库里取出一批数据，最后根据数据统计返回计算结果，数据量上百亿以后，这样的处理方式肯定快不起来。若能先计算好结果，需要的时候直接返回结果不就快很多了么？这种提前算好结果的方式在业内成为流计算，其主要过程为：数据如同在水管中流过，在水管的某一处或几处设置处理节点，每个节点把需要统计的纬度提前算好，最终把计算结果汇集到数据库存储。用户查询的时候直接取出已经计算好的结果即可。

举个例子来说明批处理计算与流处理计算两者的差异。

> 假设目前有一个需求，想知道某个球队的历史总得分。批处理类似录像回放，取出视频，看完每场回放，把每一场得分相加，就得到总分数。流处理类似有一个一直跟着球队的计分牌，球队得分了就在计分牌上加上所得分数，计分牌虽然不知道以前每场比赛的得分，但只要看一眼就知道当前总得分是多少。


虽然流式计算很“快”，使用的前提是定义好统计的纬度和各纬度的计算指标（计分牌）。沃民目前的统计纬度中除了关键词不知道有多少，其余诸如数据源、评价、类别有多少项是知道的。

关键词这一项用户可以任意输入，这就造成了关键词的数量是无限的。比如：中文常用字有3500个，任意两个字组合成一个词，便有1200万个词，更别提三个字的词或者更多字的词，统计上接近无限。我猜这就是任何第三方指数产品不能保证所有词都有指数的原因，不是他们的开发不行，而是技术上实现起来代价太大了。

任何系统都不是万能的，资源也是有限的。响应速度提升了，其他地方就肯定有所牺牲。鱼与熊掌不可得兼，是需要绝对保证任何一个关键词都有统计结果返回，还是需要保证大部分关键词的统计结果得到快速响应。若此系统的功能需要保证前者，那流式计算的方案也就无用武之地。

**五、改进建议方案评估**

若能接受有限关键词的统计这一限制，接下来我们分析一下一百万个关键词够不够，以下是几项数据：

 - 搜狗输入法词库有近40万个关键词。
 - 汉语常用词库有近30万个关键词。
 - 百度搜索词库有50万个关键词。

加上一些行业特有的词库，所有词库合并去重后不超过70万个关键词。总结下来100万个关键词应付大多数场景足够了。

这样做的好处也很明显。当用户在页面上体验我们产品的时候，页面可适当提供一些常用词，直接点击这些词即可返回迅速返回统计结果。若用户搜索了词库里不存在词，可对他进行提示，由数据库管理人员开通对此关键词的分析。另一方面也减少了类似dos攻击的可能（比如若有恶意用户去气象台提交大量实际生活中不存在的关键词，那服务器全在进行无效计算，基本上所有服务会全部出现崩溃）。


接下来对需要处理的数据量级做一个分析。

处理的是类似新闻的资讯数据，对单条数据需要的统计纬度有：时间、关键词、数据源（一级分类9个，二级分类27个）、评价（3个）、类别（5个）。一个词在统计的单位时间内最多产生`27*3*5=405`条统计数据。假设词库有100万，所有词在统计的单位时间内最多产生4亿条统计数据。

若时间粒度按天聚合统计，则一天最多4亿条统计信息。按照小时聚合统计，一天最多96亿条统计信息。时间精度高，数据量大，查询就慢。为了平衡精度和性能，最近24小时内的数据可按小时统计，数据量24小时最多为96亿条。超过24小时的数据按天统计。一年最多1460亿条统计数据。

时序数据库如DolphinDB、InfluxDB、OpenTSDB，天然适合解决类似统计信息的聚合需求。目前开源的时序数据库系统，使用单机64G内存，针对百亿级别的数据量，响应速度可优化到秒级别。超过千亿以后基本需要考虑收费方案。

综上所述，若使用流计算平台提前计算统计纬度，使用时序数据库提供结果查询，辅以图数据库解决印象分析，这样的架构设计差不多能解决70%左右的需求场景。

当然，任何技术只是工具，只要满足需求，用什么工具都行，甚至流式计算和批处理同时使用也未尝不可。用户查询时，在词库的关键词就调用流式计算返回结果，不在词库的关键词调用批处理返回结果，对于前端用户来说基本是无感知的。


**六、总结**

因为时间的关系，以及对需求的不了解，还有很多方面的设计没考虑到，一些技术的性能也来不及测试，很多技术细节也无法在一篇文字中解释清楚，上面所说肯定有很多考虑不周全的地方。

欢迎关注微信公众号「**下课了**」一起交流。














