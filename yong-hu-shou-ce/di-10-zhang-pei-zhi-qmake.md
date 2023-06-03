# 第10章 配置qmake

## 属性 <a href="#_toc1739" id="_toc1739"></a>

qmake有一个持久性配置系统，它允许您在qmake中设置一次属性，并在每次调用qmake时查询它。您可以在qmake中设置如下属性：

qmake -set PROPERTY VALUE

用适当的属性和值来替换上述PROPERTY和VALUE。

您可以从qmake中检索回此信息，如下所示：

qmake -query PROPERTY

qmake -query #查询当前全部PROPERTY/VALUE对

注：除了使用qmake-set PROPERTY VALUE设置的属性之外，qmake -query还列出了内置的属性。

这些信息将保存到QSettings对象中（这意味着它将存储在不同平台的不同位置）。

以下列表总结了内置属性：

* QMAKE\_SPEC - 在主机构建期间被解析并存储在QMAKESPEC变量中的主机mkspec的短名
* QMAKE\_VERSION - qmake的当前版本
* QMAKE\_XSPEC - 在目标构建期间被解析并存储在QMAKESPEC变量中的目标mkspec的短名
* QT\_HOST\_BINS - 主机可执行文件的位置
* QT\_HOST\_DATA - qmake使用的主机可执行文件的数据位置
* QT\_HOST\_PREFIX - 所有主机路径的默认前缀
* QT\_INSTALL\_ARCHDATA - 依赖于一般架构的Qt数据的位置
* QT\_INSTALL\_BINS - Qt的二进制文件（工具和应用程序）的位置
* QT\_INSTALL\_CONFIGURATION - 针对Qt设置的位置。不适用于Windows
* QT\_INSTALL\_DATA - 通用架构独立的Qt数据的位置
* QT\_INSTALL\_DOCS - 文档的位置
* QT\_INSTALL\_EXAMPLES - 实例的位置
* QT\_INSTALL\_HEADERS - 针对所有头文件的位置
* QT\_INSTALL\_IMPORTS - QML 1.x的扩展
* QT\_INSTALL\_LIBEXECS - 库在运行时需要的可执行文件的位置
* QT\_INSTALL\_LIBS - 存储库的位置
* QT\_INSTALL\_PLUGINS - Qt插件的位置
* QT\_INSTALL\_PREFIX - 所有路径的默认前缀
* QT\_INSTALL\_QML - QML2.x的扩展
* QT\_INSTALL\_TESTS - Qt测试用例的位置
* QT\_INSTALL\_TRANSLATIONS - Qt字符串的翻译信息的位置
* QT\_SYSROOT - 目标构建环境使用的系统根
* QT\_VERSION - Qt版本。我们建议您使用\$$QT.\<module>.version变量来查询Qt模块特定的版本号。

例如，您可以使用QT\_INSTALL\_PREFIX属性查询这个版本的qmake的Qt的安装信息：

qmake -query "QT\_INSTALL\_PREFIX"

您可以按以下方式查询项目文件中的属性值：

QMAKE\_VERS = \$$\[QMAKE\_VERSION]

## QMAKESPEC <a href="#_toc28078" id="_toc28078"></a>

qmake需要一个平台和编译器描述文件，其中包含许多用于生成适当的Makefile的默认值。标准的Qt发行版附带了许多这样的文件，它们位于Qt安装的mkspecs子目录中。

QMAKESPEC环境变量可以包含以下任何一个内容：

* 指向包含qmake.conf文件的目录的完整路径。在这种情况下，qmake将从该目录中打开qmake.conf文件。如果该文件不存在，则qmake将退出并出错。
* 平台-编译器组合的名称。在这种情况下，qmake将在编译Qt时指定的数据路径的mkspecs子目录中指定的目录(参见QLibraryInfo::DataPath)。

注：QMAKESPEC路径将自动添加到INCLUDEPATH系统变量的内容后生成的Makefile中。

## 缓存文件 <a href="#_toc2752" id="_toc2752"></a>

缓存文件是一个特殊的文件，读取qmake用于查找在qmake.conf文件、项目文件或命令行中未指定的设置。运行“qmake”时，它会在当前目录的父目录中查找一个名为.qmake.cache的文件，除非您指定-nocache。如果qmake未能找到这个文件，它将静默地忽略此处理步骤。

如果qmake找到了一个.qmake.cache文件，那么它将在处理项目文件之前首先处理这个文件。

## 文件扩展 <a href="#_toc26253" id="_toc26253"></a>

在正常情况下，qmake将尝试为您的平台使用适当的文件扩展名。但是，有时需要覆盖每个平台的默认选择，并显式地定义供qmake使用的文件扩展名。这是通过重新定义某些内置变量来实现的。例如，用于moc文件的扩展名可以通过项目文件中的以下分配来重新定义：

以下变量可用于重新定义由qmake识别的通用文件扩展名：

* QMAKE\_EXT\_MOC - 更改放置在已包含的moc文件上的扩展名。
* QMAKE\_EXT\_UI - 更改用于Qt设计师UI文件的扩展名（通常是在FORMS中）。
* QMAKE\_EXT\_PRL - 更改位于库依赖文件上的扩展名。
* QMAKE\_EXT\_LEX - 更改在Lex文件中使用的后缀（通常是在LEXSOURCES中）。
* QMAKE\_EXT\_YACC - 更改Yacc文件中使用的后缀（通常在YACCSOURCES中）。
* QMAKE\_EXT\_OBJ - 更改在生成的对象文件上使用的后缀。

以上所有内容都只接受第一个值，因此您必须只为它分配一个将在整个项目文件中使用的值。有两个变量接受一个值列表：

* QMAKE\_EXT\_CPP - 导致qmake将所有带有这些后缀的文件解释为C++源文件。
* QMAKE\_EXT\_H - 导致qmake将所有带有这些后缀的文件解释为C和C++头文件。
