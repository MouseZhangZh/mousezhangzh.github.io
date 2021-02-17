---
layout: post
title: "中文分词库简要对比"
excerpt: "再搞分词就转NLP辣！！！"
categories: [Debian, C++, Qt]
comments: true
---

# ukui-search遇到的问题：

## 目前来看内存占用过多主要由几个方面构成

1. 中文分词库占用内存过多（主要）
2. 处理大量数据时Qt可能会有内存释放不完全的问题（经测试，向QString中写入大量数据后，无法完全释放内存，原因暂时未知，目前使用多进程规避）
3. 处理数据时可能会有多余的内存拷贝（零零碎碎，不会太多）
4. xapian也有可能存在内存不释放，也暂时通过多进程规避

## 分词库的选择
jieba分词由Python开发而成，由其他开发者移植到了C++，或许他的分词效果很好，但速度和内存占用远远超过了我们的承受范围，趁春节几天算是试图入门NLP，也找找分词工具。

[准确实用，7个优秀的开源中文分词库推荐](https://blog.csdn.net/liyongzhi1992/article/details/81316815)
这位博主介绍了7个分词库，一一来看：

1. [jieba分词](https://github.com/fxsjy/jieba)

自己点链接看吧，链接是原版，用Python开发，后面有开发者用其他语言实现，我们就使用了cppjieba，其速度与内存超出了我们的承受范围。

2. [HanLP](https://www.hanlp.com/)

看到HanLP提供了汉字转拼音接口，就随手试了一下。地址是[https://www.hanlp.com/demonstrate.html](https://www.hanlp.com/demonstrate.html)，文本输入框中输入`翟康宁`，功能选择`拼音转换/汉字转拼音`，点击发送，返回结果如下：
```json
{
    "code": 0,
    "data": [
        {
            "head": "d",
            "pinyinWithToneMark": "dí",
            "pinyinWithoutTone": "di",
            "shengmu": "d",
            "tone": 2,
            "yunmu": "i"
        },
        {
            "head": "k",
            "pinyinWithToneMark": "kāng",
            "pinyinWithoutTone": "kang",
            "shengmu": "k",
            "tone": 1,
            "yunmu": "ang"
        },
        {
            "head": "n",
            "pinyinWithToneMark": "níng",
            "pinyinWithoutTone": "ning",
            "shengmu": "n",
            "tone": 2,
            "yunmu": "ing"
        }
    ]
}
```
或许人名支持的不好，再试试多音字，这次发送的内容填`仇姓来源于春秋宋国大夫仇牧`，看看结果是什么：
```
{
    "code": 0,
    "data": [
        {
            "head": "q",
            "pinyinWithToneMark": "qiú",
            "pinyinWithoutTone": "qiu",
            "shengmu": "q",
            "tone": 2,
            "yunmu": "iu"
        },
        {
            "head": "x",
            "pinyinWithToneMark": "xìng",
            "pinyinWithoutTone": "xing",
            "shengmu": "x",
            "tone": 4,
            "yunmu": "ing"
        },
        {
            "head": "l",
            "pinyinWithToneMark": "lái",
            "pinyinWithoutTone": "lai",
            "shengmu": "l",
            "tone": 2,
            "yunmu": "ai"
        },
        {
            "head": "y",
            "pinyinWithToneMark": "yuán",
            "pinyinWithoutTone": "yuan",
            "shengmu": "y",
            "tone": 2,
            "yunmu": "uan"
        },
        {
            "head": "y",
            "pinyinWithToneMark": "yú",
            "pinyinWithoutTone": "yu",
            "shengmu": "y",
            "tone": 2,
            "yunmu": "u"
        },
        {
            "head": "ch",
            "pinyinWithToneMark": "chūn",
            "pinyinWithoutTone": "chun",
            "shengmu": "ch",
            "tone": 1,
            "yunmu": "un"
        },
        {
            "head": "q",
            "pinyinWithToneMark": "qiū",
            "pinyinWithoutTone": "qiu",
            "shengmu": "q",
            "tone": 1,
            "yunmu": "iu"
        },
        {
            "head": "s",
            "pinyinWithToneMark": "sòng",
            "pinyinWithoutTone": "song",
            "shengmu": "s",
            "tone": 4,
            "yunmu": "ong"
        },
        {
            "head": "g",
            "pinyinWithToneMark": "guó",
            "pinyinWithoutTone": "guo",
            "shengmu": "g",
            "tone": 2,
            "yunmu": "uo"
        },
        {
            "head": "d",
            "pinyinWithToneMark": "dài",
            "pinyinWithoutTone": "dai",
            "shengmu": "d",
            "tone": 4,
            "yunmu": "ai"
        },
        {
            "head": "f",
            "pinyinWithToneMark": "fū",
            "pinyinWithoutTone": "fu",
            "shengmu": "f",
            "tone": 1,
            "yunmu": "u"
        },
        {
            "head": "ch",
            "pinyinWithToneMark": "chóu",
            "pinyinWithoutTone": "chou",
            "shengmu": "ch",
            "tone": 2,
            "yunmu": "ou"
        },
        {
            "head": "m",
            "pinyinWithToneMark": "mù",
            "pinyinWithoutTone": "mu",
            "shengmu": "m",
            "tone": 4,
            "yunmu": "u"
        }
    ]
}
```

第一个仇被识别出来了，第二个仇没有。

再试试繁体字（主要是看看他有几级字库），这次填`長太息以掩涕兮，哀民生之多艱。余雖好修姱以鞿羁兮，謇朝谇而夕替。`，返回内容如下：
```
{
    "code": 0,
    "data": [
        {
            "head": "ch",
            "pinyinWithToneMark": "cháng",
            "pinyinWithoutTone": "chang",
            "shengmu": "ch",
            "tone": 2,
            "yunmu": "ang"
        },
        {
            "head": "t",
            "pinyinWithToneMark": "tài",
            "pinyinWithoutTone": "tai",
            "shengmu": "t",
            "tone": 4,
            "yunmu": "ai"
        },
        {
            "head": "x",
            "pinyinWithToneMark": "xī",
            "pinyinWithoutTone": "xi",
            "shengmu": "x",
            "tone": 1,
            "yunmu": "i"
        },
        {
            "head": "y",
            "pinyinWithToneMark": "yǐ",
            "pinyinWithoutTone": "yi",
            "shengmu": "y",
            "tone": 3,
            "yunmu": "i"
        },
        {
            "head": "y",
            "pinyinWithToneMark": "yǎn",
            "pinyinWithoutTone": "yan",
            "shengmu": "y",
            "tone": 3,
            "yunmu": "an"
        },
        {
            "head": "t",
            "pinyinWithToneMark": "tì",
            "pinyinWithoutTone": "ti",
            "shengmu": "t",
            "tone": 4,
            "yunmu": "i"
        },
        {
            "head": "x",
            "pinyinWithToneMark": "xī",
            "pinyinWithoutTone": "xi",
            "shengmu": "x",
            "tone": 1,
            "yunmu": "i"
        },
        {
            "head": "none",
            "pinyinWithToneMark": "none",
            "pinyinWithoutTone": "none",
            "shengmu": "none",
            "tone": 5,
            "yunmu": "none"
        },
        {
            "head": "a",
            "pinyinWithToneMark": "āi",
            "pinyinWithoutTone": "ai",
            "shengmu": "none",
            "tone": 1,
            "yunmu": "ai"
        },
        {
            "head": "m",
            "pinyinWithToneMark": "mín",
            "pinyinWithoutTone": "min",
            "shengmu": "m",
            "tone": 2,
            "yunmu": "in"
        },
        {
            "head": "sh",
            "pinyinWithToneMark": "shēng",
            "pinyinWithoutTone": "sheng",
            "shengmu": "sh",
            "tone": 1,
            "yunmu": "eng"
        },
        {
            "head": "zh",
            "pinyinWithToneMark": "zhī",
            "pinyinWithoutTone": "zhi",
            "shengmu": "zh",
            "tone": 1,
            "yunmu": "i"
        },
        {
            "head": "d",
            "pinyinWithToneMark": "duō",
            "pinyinWithoutTone": "duo",
            "shengmu": "d",
            "tone": 1,
            "yunmu": "uo"
        },
        {
            "head": "j",
            "pinyinWithToneMark": "jiān",
            "pinyinWithoutTone": "jian",
            "shengmu": "j",
            "tone": 1,
            "yunmu": "ian"
        },
        {
            "head": "none",
            "pinyinWithToneMark": "none",
            "pinyinWithoutTone": "none",
            "shengmu": "none",
            "tone": 5,
            "yunmu": "none"
        },
        {
            "head": "y",
            "pinyinWithToneMark": "yú",
            "pinyinWithoutTone": "yu",
            "shengmu": "y",
            "tone": 2,
            "yunmu": "u"
        },
        {
            "head": "s",
            "pinyinWithToneMark": "suī",
            "pinyinWithoutTone": "sui",
            "shengmu": "s",
            "tone": 1,
            "yunmu": "ui"
        },
        {
            "head": "h",
            "pinyinWithToneMark": "hǎo",
            "pinyinWithoutTone": "hao",
            "shengmu": "h",
            "tone": 3,
            "yunmu": "ao"
        },
        {
            "head": "x",
            "pinyinWithToneMark": "xiū",
            "pinyinWithoutTone": "xiu",
            "shengmu": "x",
            "tone": 1,
            "yunmu": "iu"
        },
        {
            "head": "k",
            "pinyinWithToneMark": "kuā",
            "pinyinWithoutTone": "kua",
            "shengmu": "k",
            "tone": 1,
            "yunmu": "ua"
        },
        {
            "head": "y",
            "pinyinWithToneMark": "yǐ",
            "pinyinWithoutTone": "yi",
            "shengmu": "y",
            "tone": 3,
            "yunmu": "i"
        },
        {
            "head": "j",
            "pinyinWithToneMark": "jī",
            "pinyinWithoutTone": "ji",
            "shengmu": "j",
            "tone": 1,
            "yunmu": "i"
        },
        {
            "head": "j",
            "pinyinWithToneMark": "jī",
            "pinyinWithoutTone": "ji",
            "shengmu": "j",
            "tone": 1,
            "yunmu": "i"
        },
        {
            "head": "x",
            "pinyinWithToneMark": "xī",
            "pinyinWithoutTone": "xi",
            "shengmu": "x",
            "tone": 1,
            "yunmu": "i"
        },
        {
            "head": "none",
            "pinyinWithToneMark": "none",
            "pinyinWithoutTone": "none",
            "shengmu": "none",
            "tone": 5,
            "yunmu": "none"
        },
        {
            "head": "j",
            "pinyinWithToneMark": "jiǎn",
            "pinyinWithoutTone": "jian",
            "shengmu": "j",
            "tone": 3,
            "yunmu": "ian"
        },
        {
            "head": "ch",
            "pinyinWithToneMark": "cháo",
            "pinyinWithoutTone": "chao",
            "shengmu": "ch",
            "tone": 2,
            "yunmu": "ao"
        },
        {
            "head": "s",
            "pinyinWithToneMark": "suì",
            "pinyinWithoutTone": "sui",
            "shengmu": "s",
            "tone": 4,
            "yunmu": "ui"
        },
        {
            "head": "e",
            "pinyinWithToneMark": "ér",
            "pinyinWithoutTone": "er",
            "shengmu": "none",
            "tone": 2,
            "yunmu": "er"
        },
        {
            "head": "x",
            "pinyinWithToneMark": "xī",
            "pinyinWithoutTone": "xi",
            "shengmu": "x",
            "tone": 1,
            "yunmu": "i"
        },
        {
            "head": "t",
            "pinyinWithToneMark": "tì",
            "pinyinWithoutTone": "ti",
            "shengmu": "t",
            "tone": 4,
            "yunmu": "i"
        },
        {
            "head": "none",
            "pinyinWithToneMark": "none",
            "pinyinWithoutTone": "none",
            "shengmu": "none",
            "tone": 5,
            "yunmu": "none"
        }
    ]
}
```
繁体字可以识别，但`好`和`朝`的音是错的，果然多音字是一大问题。。。

回到分词，《自然语言处理入门》是本好书，对于学习NLP有很大帮助，其作者何晗老师的HanLP也很强大，可惜没有C/C++实现，而且内存占用最少120MB（数据来自官网）。

3. [jcseg](https://gitee.com/lionsoul/jcseg)

作者狮子的魂，friso也是他写的，看看人家。不过看了一下这个项目之后突然觉得friso是阉割版，只有3种模式，而jcseg有7中模式，特别是最多模式，NLP模式和n-gram模式，MMSEG要学习学习，~~争取搞个C++版的~~。

后面的几个不想看了，都是go和Java的，不可能因为这个分词搞个JDK上去，还有几个C/C++的分词库：[ICTCLAS](https://github.com/NLPIR-team/NLPIR)，中科院出品，这个很牛逼，但商用要恰钱的。

[c++ 中文分词介绍](https://blog.csdn.net/zhulinu/article/details/8630360)，这篇文章介绍了几种C++的中文分词库，有中科院的分词、LibMMSeg、SCWS等等。怕他哪一天没了，粘贴过来。

> 本文不是专业的介绍中文的知识，只是由于项目上可能需要中文分词，在网上找了一些资料，再次记录一下。
主要参考网站是oschina里面收录的内容：
中科院中文分词  ICTCLAS
这个据说效率挺高，但不是纯开源版本，里面有个文章12年7月1日失效，就是由于授权协议失效，所以要用到这个类库的时候要小心了，当然你可以购买版权。
还有一个重要的问题是官网打不开。http://www. ictclas .org
协议未知
中文分词软件包  LibMMSeg
LibMMSeg 是Coreseek.com为 Sphinx 全文搜索引擎设计的中文分词软件包，其在 GPL协议 下发行的中文分词法，采用 Chih-Hao Tsai 的MMSEG算法。
LibMMSeg 采用C++开发，同时支持Linux平台和Windows平台，切分速度大约在500K/s（酷睿 2.4G）；截至目前版本LibMMSeg没有为速度做过特殊优化，进一步的提升切分速度仍有空间。
官方网站http://www.coreseek.cn/opensource/mmseg/
简易中文分词系统  SCWS
这是一套基于词频词典的机械中文分词引擎，采用的是自行采集的词频词典，并辅以一定程度上的专有名称、人名、地名、数字年代等规则集， SCWS 采用纯 C 代码开发，以 Unix-Like OS 为主要平台环境，提供共享函数库，方便植入各种现有软件系统。此外它支持 GBK，UTF-8，BIG5 等汉字编码
效率：准确: 95%, 召回: 91%, 速度: 1.2MB/sec 
协议： BSD 许可协议开源发布
最新版本 2013-1-15: SCWS-1.2.1 Released.
中文句法分析器 ctbparser
一个用C++实现的 中文句法分析 工具包，采用的是中文宾州树库标准(Chinese Tree Bank)，该句法分析工具采用了标准的图模型算法，即最大生成树算法(projective Maximum Spanning Tree)。
效率：ctbparser分词得到95.3% F1值，词性标注精度94.27%，句法分析精度81%。处理速度（包括分词、词性标注、句法分析）的速度是每秒30句，内存占用为270M。（操作系 统：64位CentOS 5，CPU: Intel(R) Xeon(R) E5405, 2.00GHz）
授权协议： LGPL
最新版本：2012-2-3 0.12版本
中文分词开源版 CRF
CRF中文分词开源版仅仅包含CRF++软件包中分词解码器部分，简化了CRF++复杂代码结构，清除了分词解码器不需要的代码，大大提高了分词解码器的可读性和可懂度。
技术支持：http://langiner.blog.51cto.com/  2010-08-20之后没有更新
授权协议：非开源
最新版本未知
中文分词库 NlpBamboo
bamboo是一个中文语言处理系统。目前包括中文分词和词性标注部分。算法字构词的分词方法CRF++。
效率未知
网站：http://code.google.com/p/nlpbamboo/
授权协议： BSD
最新版本 ：2010-10 1.1.1版本，官方说明短期后面不会更新
C语言开源高性能中文分词器 friso
friso中文分词器friso是使用c语言开发的一个开源中文分词器，使用流行的mmseg算法实现。
1。目前最高版本：friso 0.1，只支持UTF-8编码。【源码无需修改就能在各种平台下编译使用，加载完20万的词条，内存占用稳定为14M。】。
2。mmseg四种过滤算法，分词准确率达到了98.41%。
3。支持自定义词库。在dict文件夹下，可以随便添加/删除/更改词库和词库词条，并且对词库进行了分类。
4。词库使用了friso的Java版本jcseg的简化词库。
5。支持中英混合词的识别。例如：c语言，IC卡。
7。很好的英文支持，电子邮件，网址，小数，分数，百分数。
8。支持阿拉伯数字基本单字单位的识别，例如2012年，5吨，120斤。
9。自动英文圆角/半角，大写/小写转换。
二。分词速度
测试环境：2.8GHZ/2G/Ubuntu
简单模式：3.7M/秒
复杂模式：1.8M/秒
授权协议： LGPL
最新版本：2012-12-28日 
综合来看
简易中文分词系统 SCWS和C语言开源高性能中文分词器 friso是在最近更新的，应该最近会有支持。
仅仅是个人理解。
下面是网上的一个人的评论：
但市场上提供免费甚至开源的分词引擎不多，中科院研发的ictclas30分词精确度和分词速度都非常不错，而且还有词性标注和自定义添加词的功能，可惜不开源。另外比较受欢迎的还有libmmseg和SCWS，因此都是开源的，不过经测试libmmseg的分词精度似乎不高，而SCWS由于使用了大量的递归，在生成词库的时候经常导致栈溢出（我是用vc2005编译的），需要自己将递归修改为循环，从演示的情况来看，SCWS的分词精度来算可以。
附录：编码转换
最新版的iconv（目前是libiconv-1.14.tar.gz）已经不再提供Visual C++的编译文件，最后一个支持VC编译的iconv版本是libiconv-1.11.1.tar.gz。虽然不是最新版，但是在Windows下编译，只好将就了，功能应该差不了多少。
下载libiconv-1.11.1.tar.gz并解压，打开命令提示行，进入libiconv-1.11.1目录，输入命令：
nmake -f Makefile.msvc NO_NLS=1 MFLAGS=-MD
nmake -f Makefile.msvc NO_NLS=1 MFLAGS=-MD PREFIX=C:\iconv install

懒得排版了，就酱8。。。
综合来看SCWS、LibMMSEG、friso这三个比较符合要求，但根据上面的博主的测试来看，SCWS需要把递归改成for循环，而且递归的效率。。。因为ukui-search是搜索工具，对算法的精度没有那么高（但最好也别太低），LibMMSEG上面也说了速度有待优化，如果博主说的没错的话，就暂定为friso，对于friso的源码的阅读与理解，后面再补。

## QString为什么不能完全释放内存

这个占个坑，估计弄明白要另搞一篇。。。

## 多余的内存拷贝

这个属于对C++的使用和理解了，占坑

## 关于xapian的详细内容

占坑+1
