# 第1章 概述

qmake工具为您提供了一个面向项目的系统，它用于管理应用程序、库和其他组件的构建过程。这种方法允许您控制所使用的源文件，并允许简洁地描述每个步骤过程，通常是在单个文件中。qmake将每个项目文件中的信息扩展为一个Makefile，该文件执行编译和链接所需的命令。

## 描述项目 <a href="#_toc4048" id="_toc4048"></a>

项目由项目（.pro）文件的内容来描述。qmake使用文件中的信息来生成包含构建每个项目所需的所有命令的Makefile。项目文件通常包含一个源文件和头文件的列表、一般配置信息以及任何特定于应用程序的细节，例如要链接的额外库列表，或要使用的额外包含路径列表。

项目文件可以包含许多不同的元素，包括注释、变量声明、内置函数和一些简单的控制结构。在大多数简单的项目中，只需要声明用于使用一些基本配置选项构建项目的源文件和头文件。有关如何创建简单项目文件的更多信息，请参阅[入门](di-2-zhang-ru-men.md)。

您可以为复杂的项目创建更复杂的项目文件。有关项目文件的概述，请参见[创建项目文件](di-3-zhang-chuang-jian-xiang-mu-wen-jian.md)。有关可以在项目文件中使用的变量和函数的详细信息，请见[参考](broken-reference)。

您可以使用应用程序或库项目模板来指定专门的配置选项来微调构建过程。有关更多信息，请参见[构建公共项目类型](di-4-zhang-gou-jian-tong-yong-xiang-mu-lei-xing.md)。

您可以使用Qt Creator new project wizard来创建项目文件。如果您选择了项目模板，那么Qt Creator将创建一个具有默认值的项目文件，从而使您能够构建和运行该项目。您可以修改项目文件以匹配您的目的。

您还可以使用qmake来生成项目文件。有关qmake命令行选项的完整描述，请参阅[运行qmake](di-5-zhang-yun-hang-qmake.md)。

qmake的基本配置特性可以处理大多数跨平台的项目。但是，使用一些特定于平台的变量可能是有用的，甚至是必要的。有关更多信息，请参见[平台说明](di-6-zhang-xi-tong-ping-tai-shuo-ming.md)。

## 构建项目 <a href="#_toc12692" id="_toc12692"></a>

对于简单的项目，您只需要在项目的顶级目录中运行qmake来生成Makefile。然后，您可以运行平台的make工具，根据Makefile来构建项目。

有关qmake在配置构建过程时使用的环境变量的更多信息，请参见配置qmake。

## 使用第三方库 <a href="#_toc2539" id="_toc2539"></a>

[Third Party Libraries ](http://qt-project.org/doc/qt-5/third-party-libraries.html)指南向您展示了如何在Qt项目中使用简单的第三方库。

## 预编译头 <a href="#_toc30032" id="_toc30032"></a>

在大型项目中，可以利用预编译的头文件来加快构建过程。有关更多信息，请参见[使用预编译头](di-9-zhang-shi-yong-yu-bian-yi-de-tou-wen-jian.md)。
