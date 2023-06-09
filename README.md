---
description: 基于Qt5.15版本翻译 | By @tyw66
cover: .gitbook/assets/cover(1).jpg
coverY: 12
---

# QMake官方文档【翻译】

<mark style="color:red;">**版权申明：本作品采用知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议进行许可。（https://creativecommons.org/licenses/by-nc-sa/4.0/）**</mark>

<mark style="color:blue;">>> 基于Qt5.15版本翻译，原文链接：</mark>[<mark style="color:blue;">https://doc.qt.io/qt-5/qmake-manual.html</mark>](https://doc.qt.io/qt-5/qmake-manual.html)

<mark style="color:blue;">>> 本文档基于Gitbook编写，本文档链接：</mark>[<mark style="color:blue;">https://tyw66.gitbook.io/qmake\_manual\_cn/</mark>](https://tyw66.gitbook.io/qmake\_manual\_cn/)

## 第Ⅰ部分 · 用户手册

QMake工具有助于简化跨不同平台的开发项目的构建过程。它可以自动生成Makefile，而创建每个Makefile只需要几行信息。您可以对任何软件项目使用qmake，无论它是否用Qt编写。

根据项目文件中的信息生成一个Makefile。项目文件由开发人员创建，通常很简单，但是可以为复杂的项目创建更复杂的项目文件。

qmake包含支持Qt开发的附加特性，可以自动包括moc和uic的构建规则。

qmake还可以为Microsoft Visual studio生成项目，而不需要开发人员更改项目文件。



**内容**

[第1章 概述](yong-hu-shou-ce/di-1-zhang-gai-shu.md)&#x20;

[第2章 入门 ](yong-hu-shou-ce/di-2-zhang-ru-men.md)

[第3章 创建项目文件 ](yong-hu-shou-ce/di-3-zhang-chuang-jian-xiang-mu-wen-jian.md)

[第4章 构建通用项目类型 ](yong-hu-shou-ce/di-4-zhang-gou-jian-tong-yong-xiang-mu-lei-xing.md)

[第5章 运行qmake](yong-hu-shou-ce/di-5-zhang-yun-hang-qmake.md)&#x20;

[第6章 系统平台说明 ](yong-hu-shou-ce/di-6-zhang-xi-tong-ping-tai-shuo-ming.md)

[第7章 qmake语言 ](yong-hu-shou-ce/di-7-zhang-qmake-yu-yan.md)

[第8章 高级使用 ](yong-hu-shou-ce/di-8-zhang-gao-ji-shi-yong.md)

[第9章 使用预编译的标题 ](yong-hu-shou-ce/di-9-zhang-shi-yong-yu-bian-yi-de-tou-wen-jian.md)

[第10章 配置qmake ](yong-hu-shou-ce/di-10-zhang-pei-zhi-qmake.md)



## 第Ⅱ部分 · 参考手册

本部分详细描述了在qmake项目文件中可供使用的变量和函数。

变量参考：描述了qmake在配置项目的构建过程时可以识别的变量。

函数参考：qmake函数有两种类型：替换函数和测试函数。替换函数返回一个值的列表，而测试函数返回布尔结果。这些函数在两个地方实现：基本功能是作为内置函数提供的。更复杂的函数在特性文件库(.prf)中实现。这些函数按其类型进行分类：替换函数、测试函数。

**内容**

[第11章 变量](can-kao-shou-ce/di-11-zhang-bian-liang.md)

[第12章 替换函数](can-kao-shou-ce/di-12-zhang-ti-huan-han-shu.md)

[第13章 测试函数](can-kao-shou-ce/di-13-zhang-ce-shi-han-shu.md)







<div align="center" data-full-width="false">

<figure><img src=".gitbook/assets/分隔素材.png" alt="译者的分割线"><figcaption></figcaption></figure>

</div>

<mark style="color:green;">Tips：</mark>

* 如果您是初次接触Qt或者QMake，建议通读第1-5章，对qmake有一个大致的了解。
* 第5-10章描述了不同的主题，按需进行阅读，或者有一定使用经验后通读，进行查漏补缺。
* 参考手册即第11-13章，是字典的性质，可以查询相关的变量与函数，随用随查。
* 如在阅读过程中发现问题，欢迎来[github](https://github.com/tyw66/QMakeManual\_CN/issues)或者[知乎](https://zhuanlan.zhihu.com/p/634557870?)告诉我。







:writing\_hand:原创翻译与校对，如您觉得有所收获，可以请作者吃个鸡腿🍗，鼓励创作出更多内容

<div align="left">

<figure><img src=".gitbook/assets/WX收款码.jpg" alt="" width="188"><figcaption></figcaption></figure>

 

<figure><img src=".gitbook/assets/zfb收款码.jpg" alt="" width="188"><figcaption></figcaption></figure>

</div>

