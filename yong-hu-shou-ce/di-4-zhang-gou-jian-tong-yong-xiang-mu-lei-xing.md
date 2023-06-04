# 第4章 构建通用项目类型

本章介绍如何为基于Qt的三种常见项目类型设置qmake项目文件：应用程序、库和插件。尽管所有项目类型都使用许多相同的变量，但每个变量都使用特定于项目的变量来自定义输出文件。

这里没有描述平台特定的变量。有关这部分的更多信息，请参见[Qt for Windows - Deployment](https://doc.qt.io/qt-5/windows-deployment.html)和[Qt for macOS](https://doc.qt.io/qt-5/macos.html)。

## 构建应用程序 <a href="#_toc31112" id="_toc31112"></a>

应用程序模板告诉qmake生成一个将构建一个应用程序的Makefile。使用此模板，可以通过向`CONFIG`变量定义中添加以下选项之一来指定应用程序的类型：

<table><thead><tr><th width="178">选项</th><th>描述</th></tr></thead><tbody><tr><td>windows</td><td>该应用程序是一个Windows GUI应用程序。</td></tr><tr><td>console</td><td>仅限<code>app</code>模板：该应用程序是一个Windows控制台应用程序。</td></tr><tr><td>testcase</td><td>该应用程序是一个<a href="di-4-zhang-gou-jian-tong-yong-xiang-mu-lei-xing.md#_toc7489">自动化的测试</a>。</td></tr></tbody></table>

当使用此模板时，将识别以下qmake系统变量。您应该在.pro文件中使用这些文件来指定有关应用程序的信息。对于其他依赖于平台的系统变量，您可以查看[系统平台说明](di-6-zhang-xi-tong-ping-tai-shuo-ming.md)。

* `HEADERS` - 此应用程序的c++头文件的列表。
* `SOURCES` - 此应用程序的c++源文件的列表。
* `FORMS` - 此应用程序的ui文件列表（使用Qt设计师创建）
* `LEXSOURCES` - 此应用程序的Lex源文件的列表。
* `YACCSOURCES` - 此应用程序的Yacc源文件的列表。
* `TARGET` - 此应用程序的可执行文件的名称。这将默认设置为项目文件的名称。（如果有扩展，则会自动添加该扩展）。
* `DESTDIR` - 可执行文件所在的目录。
* `DEFFINES` - 包含该应用程序所需的任何附加预处理器定义的列表。
* `INCLUDEPATH` - 应用程序所需的任何附加包含路径的列表。
* `DEPENDPATH` - 应用程序的依赖关系搜索路径。
* `VPATH`-查找所提供的文件的搜索路径。
* `DEF_FILE` - 仅Windows：为应用程序链接的一个.def文件。

您只需要使用你想为它赋值的系统变量。例如，如果您没有任何额外的包含路径（`INCLUDEPATH`），那么您不需要指定任何东西，qmake将会添加必要的默认值。项目文件示例可能如下：

```
TEMPLATE = app
DESTDIR = c:/helloapp
HEADERS += hello.h
SOURCES += hello.cpp
SOURCES += main.cpp
DEFINES += USE_MY_STUFF
CONFIG += release
```

对于单值的项，例如模板或目标目录，我们使用“=”；但是对于多值项，我们使用“+=”来_添加_到该类型的现有项中。使用“=”用新值替换变量值。例如，如果我们写`DEFINE=USE_MY_STUFF`，则将删除所有其他定义。

## 构建测试用例 <a href="#_toc7489" id="_toc7489"></a>

测试案例项目是一个旨在作为自动化测试运行的app项目。任何应用程序都可以通过向`CONFIG`变量中添加值`testcase`来将其标记为测试用例。

对于测试案例项目，qmake将在生成的Makefile中插入一个检查（`check`）目标。此目标将运行该应用程序。如果该测试以等于零的退出代码终止，则认为该测试已通过。

检查目标通过`SUBDIRS`项目自动递归。这意味着可以从子目录项目中发出`make check`命令来运行整个测试套件。

检查（`check`）目标的执行可以由某些Makefile变量来定制。这些变量包括：

<table><thead><tr><th width="202">变量</th><th>描述</th></tr></thead><tbody><tr><td>TESTRUNNER</td><td>每个测试命令前的命令或外壳片段。一个示例用例是一个“超时”脚本，如果测试没有在指定的时间内完成，它将终止测试。</td></tr><tr><td>TESTARGS</td><td>附加到每个测试命令中的其他参数。例如，通过传递其他参数来设置测试中的输出文件和格式（例如QTestLib支持的-o filename, format 选项）可能会很有用。</td></tr></tbody></table>

<mark style="background-color:yellow;">注：变量必须在调用</mark><mark style="background-color:yellow;">`make`</mark><mark style="background-color:yellow;">工具时设置，而不是在.pro文件中。大多数make工具都支持直接在命令行上设置Makefile变量：</mark>

```
# Run tests through test-wrapper and use xunitxml output format.
# In this example, test-wrapper is a fictional wrapper script which terminates
# a test if it does not complete within the amount of seconds set by "--timeout".
# The "-o result.xml,xunitxml" options are interpreted by QTestLib.
make check TESTRUNNER="test-wrapper --timeout 120" TESTARGS="-o result.xml,xunitxml"
```

测试案例项目可以进一步定制与以下`CONFIG`选项：

<table><thead><tr><th width="202">选项</th><th>描述</th></tr></thead><tbody><tr><td>insignificant_test</td><td>在<code>make check</code>过程中将忽略测试的退出代码。</td></tr></tbody></table>

测试用例通常用`QTest`或`TestCase`编写，但不是必需使用CONFIG+=testcase并`make check`。唯一重要的要求是，测试程序在成功时使用零退出代码退出，而在失败时使用非零退出代码退出。

## 构建库 <a href="#_toc16072" id="_toc16072"></a>

`lib`模板告诉qmake生成一个将构建一个库的Makefile。当使用此模板时，除了应用程序模板支持的系统变量外，还支持`VERSION`变量。使用.pro文件中的变量来指定有关库的信息。

当使用lib模板时，可以将以下选项添加到`CONFIG`变量中，以确定所构建的库的类型：

<table><thead><tr><th width="202">选项</th><th>描述</th></tr></thead><tbody><tr><td>dll</td><td>该库是一个共享库（dll）。</td></tr><tr><td>staticlib</td><td>该库是一个静态库。</td></tr><tr><td>plugin</td><td>这个库是一个插件。</td></tr></tbody></table>

还可以定义以下选项来提供有关该库的附加信息。

* `VERSION` - 目标库的版本号。例如：2.3.1

该库的目标文件名依赖于平台。例如，在X11、macOS和iOS上，库的名称将以`lib`为前缀。在Windows上，没有向文件名添加任何前缀。

## 构建插件 <a href="#_toc10555" id="_toc10555"></a>

插件使用lib模板构建插件，如前一节所述。这告诉qmake为项目生成一个Makefile，该项目将以适合每个平台的形式构建一个插件，通常是以库的形式。与普通库一样，版本变量用于指定有关插件的信息。

* VERSION - 目标库的版本号。例如：2.3.1

### 构建一个Qt Designer插件 <a href="#_toc26748" id="_toc26748"></a>

_Qt Designer_插件使用一组特定的配置设置构建，这些设置取决于Qt为系统的配置方式。为了方便起见，可以通过向`QT`变量中添加`designer`来启用这些设置。例如：

```
QT += widgets designer
```

有关更多基于插件的项目的示例，请参阅[Qt Designer Examples](https://doc.qt.io/qt-5/examples-designer.html)。

## 在调试和发布模式中构建和安装 <a href="#_toc20160" id="_toc20160"></a>

有时，需要在调试和发布模式下构建一个项目。尽管`CONFIG`变量可以同时保存调试和发布选项，但只应用最后指定的选项。

### 两种模式下的构建 <a href="#_toc30818" id="_toc30818"></a>

要在两种模式下构建项目，您必须向`CONFIG`变量添加`debug_and_release`选项：

```
CONFIG += debug_and_release

CONFIG(debug, debug|release) {
    TARGET = debug_binary
} else {
    TARGET = release_binary
}
```

上面代码片段中的作用域在每种模式下修改构建目标，以确保生成的目标具有不同的名称。为目标提供不同的名称可以确保其中一个名称不会覆盖另一个名称。

当qmake处理项目文件时，它将生成一个Makefile规则，以允许在两种模式下构建项目。这可以通过以下方式进行调用：

```
make all
```

`build_all`选项可以添加到项目文件中的`CONFIG`变量中，以确保默认情况下在两种模式下构建项目：

```
CONFIG+=build_all
```

这允许使用默认规则处理Makefile：

```
make
```

### 在两种模式下安装 <a href="#_toc26834" id="_toc26834"></a>

build\_all选项还确保在调用安装规则时将安装目标的两个版本：

```
make install
```

可以根据目标平台自定义构建目标的名称。例如，一个库或插件可以使用Windows上与Unix平台上使用的约定来命名：

```bash
CONFIG(debug, debug|release) {
    mac: TARGET = $$join(TARGET,,,_debug)
    win32: TARGET = $$join(TARGET,,d)
}
```

上面代码片段中的默认行为是在调试模式下构建时修改用于构建目标的名称。可以在范围中添加`else`子句，以便为发布模式执行同样的操作。如果保持原样，目标名称会保持不变。
