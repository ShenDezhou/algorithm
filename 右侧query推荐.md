#该模块对应搜狗搜索主页右侧实体推荐下面的query推荐
主要分为线下数据处理，线上服务两部分，整体框架请参考  组内分享之兴趣图谱
线上机器：
一、线下做数据
    现在这块在
    作为流程，以crontab定时任务的形式运行
    数据源主要分两部分，搜索query，用户点击title
    1. 搜索query
        入口：
        计算前一天的搜索query
        计算搜索query的特征，黄反无效过滤，计算query权重
    2. 点击title
        入口：
        计算前一天的点击title；黄反无效过滤，

二、线下CTR计算
    用于对query调权使用，ctr高的可以调整权重，在结果中固排两到三条
    入口：
    同样是crontab定时任务

三、线上Server
1. InterestHint
            线下开发环境：
            功能：该模块是Hint模块，加载线上制作好的索引文件，接收来自InterestServer的请求，返回与参数query相关的hintQuery
                       即对从getHint召回的相关query进行去同质，过滤，重排序等一些策略，最后最多保留10条最相关的结果，将结果发送到InterestServer
                      模块，以随机轮转的方式展现五条结果到前端

            关键代码：
            过滤getHint结果

四. InterestServer
            线下开发环境：
            功能：一个HttpServer，向上与TupuSearchhub模块相连，向下与InterestHint模块相连。接收来自TupuSearchhub的请求， 将请求转向InterestHint模块，得到返回的hintQuery
                      对返回的hintQuery做一些格式相关的处理后，生成XML格式，返回给TupuSearchhub模块
           关键代码：
