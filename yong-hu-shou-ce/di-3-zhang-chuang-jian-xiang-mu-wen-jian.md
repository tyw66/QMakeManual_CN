# 第3章 创建项目文件

项目文件包含qmake构建应用程序、库或插件所需的所有信息。通常，您可以使用一系列声明来指定项目中的资源，但简单的编程构造使您能够描述针对不同平台/环境的不同构建过程。

## 项目文件元素 <a href="#_toc16796" id="_toc16796"></a>

QMake所使用的项目文件格式可用于支持简单和相当复杂的构建系统。简单的项目文件使用直接的声明式样式，定义标准变量来指示项目中使用的源文件和头文件。复杂的项目可以使用控制流结构来微调构建过程。

以下部分描述了在项目文件中使用的不同类型的元素。

#### 变量 <a href="#_toc7432" id="_toc7432"></a>

在项目文件中，变量用于保存字符串的列表。在最简单的项目中，这些变量通知qmake关于要使用的配置选项，或者提供要在构建过程中使用的文件名和路径。

QMake在每个项目文件中查找某些变量，并使用这些内容来确定应该写入Makefile的内容。例如，`HEADERS`和`SOURCE`变量中的值列表用于告诉qmake关于与项目文件在同一目录中的头文件和源文件。

变量还可以在内部，用于存储临时的值列表，并且现有的值列表可以用新的值进行覆盖或扩展。

下面的代码片段说明了如何将值分配给变量：

```
HEADERS = mainwindow.h  paintwidget.h
```

变量中的值列表将以以下方式进行扩展：

```
SOURCES = main.cpp mainwindow.cpp \
          paintwidget.cpp
CONFIG += console
```

<mark style="background-color:yellow;">注：第一个分配仅包括与</mark><mark style="background-color:yellow;">`HEADERS`</mark><mark style="background-color:yellow;">变量在同一行上指定的值。第二个分配通过使用反斜杠（\）跨行分割SOURCES变量中的值。</mark>

`CONFIG`变量是qmake在生成Makefile时使用的另一个特殊变量。它在[通用配置](di-3-zhang-chuang-jian-xiang-mu-wen-jian.md#\_toc11161)中进行了讨论。在上面的代码片段中，`console`被添加到配置中包含的现有值的列表中。

下表列出了一些常用的变量，并描述了它们的内容。有关变量及其描述的完整列表，请参见[变量](../can-kao-shou-ce/di-11-zhang-bian-liang.md)。

<table><thead><tr><th width="206">变量</th><th>内容</th></tr></thead><tbody><tr><td>CONFIG</td><td>通用项目配置项。</td></tr><tr><td>DESTDIR</td><td>可执行文件或二进制文件所在的目录。</td></tr><tr><td>FORMS</td><td>需要由user interface compiler(uic)处理的UI文件的列表。</td></tr><tr><td>HEADERS</td><td>构建项目时使用的头(.h)文件的文件名列表。</td></tr><tr><td>QT</td><td>在项目中使用的Qt模块的列表。</td></tr><tr><td>RESOURCES</td><td>要包含在最终项目中的资源(.qrc)文件的列表。有关这些文件的更多信息，请参见<a href="https://doc.qt.io/qt-5/resources.html">The Qt Resource System</a>。</td></tr><tr><td>SOURCES</td><td>构建项目时使用的源代码文件列表。</td></tr><tr><td>TEMPLATE</td><td>要用于该项目的模板。这决定了构建过程的输出是应用程序、库还是插件。</td></tr></tbody></table>

一个变量的内容可以通过该变量名前附加`$$`来读取。这可以用来将一个变量的内容分配给另一个变量：

`$$`操作符广泛用于对字符串和值列表进行操作的内置函数。有关更多信息，请参见[qmake语言](di-7-zhang-qmake-yu-yan.md)。

**空格(Whitespace)**

通常，空格会在变量分配中分隔值。要指定包含空格的值，必须用双引号将这些值括起来：

引号的文本被视为变量持有的值列表中的单个项。类似的方法也用于处理包含空格的路径，特别是在为Windows平台定义包含`INCLUDEPATH`和`LIBS`变量时：

```
win32:INCLUDEPATH += "C:/mylibs/extra headers"
unix:INCLUDEPATH += "/home/user/extra headers"
```

#### 注释 <a href="#_toc17841" id="_toc17841"></a>

您可以向项目文件中添加注释。注释以`#`字符开始，并继续到同一行的末尾。例如：

```
# Comments usually start at the beginning of a line, but they
# can also follow other content on the same line.
```

要在变量赋值中包含`#`字符，必须使用内置的`LITERAL_HASH`变量的内容。

#### 内置功能和控制流程 <a href="#_toc14726" id="_toc14726"></a>

qmake提供了许多内置函数来处理变量的内容。在简单的项目文件中，最常用的函数是`include()`函数，它将一个文件名作为一个参数。给定文件的内容将会包含在项目文件中使用`include()`函数的位置。`include()`函数最常用于包含其他项目文件：

对条件结构的支持通过编程语言中的`scopes`提供，它的行为类似于编程语言中的`if`语句：

```
win32 {
    SOURCES += paintwidget_win.cpp
}
```

只有当条件为真时，才会进行大括号内的分配。在这种情况下，必须设置包含`win32`的`CONFIG`选项。这将在Windows上自动发生。左大括号必须与条件语句写在同一行。

对于通常需要循环的变量，更复杂的操作由内置函数提供，如`find()`、`unique()`和`count()`。提供了这些函数和许多其他函数来操作字符串和路径、支持用户输入和调用外部工具。有关使用这些函数的更多信息，请参见[qmake语言](di-7-zhang-qmake-yu-yan.md)。有关所有函数及其描述的列表，请参见[替换函数](../can-kao-shou-ce/di-12-zhang-ti-huan-han-shu.md)和[测试函数](../can-kao-shou-ce/di-13-zhang-ce-shi-han-shu.md)。

## 项目模板 <a href="#_toc26740" id="_toc26740"></a>

`TEMPLATE`变量用于定义要构建的项目的类型。如果没有在项目文件中声明，qmake假设应该构建的是一个应用程序，并将为此生成适当的Makefile（或等效文件）。

下表总结了可用的项目类型，并描述了qmake将为每个项目生成的文件：

<table><thead><tr><th width="155">模板</th><th>qmake输出</th></tr></thead><tbody><tr><td>app（默认）</td><td>创建一个应用程序的Makefile。</td></tr><tr><td>lib</td><td>创建一个库的Makefile。</td></tr><tr><td>aux</td><td><p>不会创建任何东西的Makefile。如果不需要调用编译器来创建目标，则可以使用此方法，例如，您的项目是用解释型语言编写的。</p><p><mark style="background-color:yellow;">注：此模板类型仅适用于基于makefile的生成器。特别是，它不能使用vcxproj和Xcode生成器。</mark></p></td></tr><tr><td>subdirs</td><td>包含使用SUBDIR变量指定的子目录规则的生成文件。每个子目录都必须包含其自己的项目文件。</td></tr><tr><td>vcapp</td><td>构建一个应用程序的VisualStudio项目文件。</td></tr><tr><td>vclib</td><td>构建一个库的VisualStudio项目文件。</td></tr><tr><td>vcsubdirs</td><td>VisualStudia解决方案文件，以在子目录中构建项目。</td></tr></tbody></table>

有关为使用app和lib模板的项目编写项目文件的建议，请参见[构建通用项目类型](di-4-zhang-gou-jian-tong-yong-xiang-mu-lei-xing.md)。

当使用`subdir`模板时，qmake生成一个Makefile来检查每个指定的子目录，处理它在那里找到的任何项目文件，并在新创建的Makefile上运行平台的make工具。`SUBDIRS`变量用于包含要处理的所有子目录的列表。

## 通用配置 <a href="#_toc11161" id="_toc11161"></a>

`CONFIG`变量指定了项目应该使用配置的选项和特性。

该项目可以在_release_模式或_debug_模式下构建，或者两者都可以同时构建。如果同时指定了两者，则后一项将生效。如果您指定了`debug_and_release`选项来同时构建项目的调试版本和发布版本，那么qmake生成的Makefile将包含一个同时构建这两个版本的规则。这可以通过以下方式进行调用：

```
make all
```

向`CONFIG`变量中添加`build_all`选项会使此规则成为构建项目时的默认规则。

<mark style="background-color:yellow;">注：在CONFIG变量中指定的每个选项也可以用作作用域的条件。您可以通过使用内置的CONFIG()函数来测试某些配置选项的存在。例如，下面的代码行展示了将函数作为条件，以测试是否只使用opengl选项：</mark>

```
CONFIG(opengl) {
    message(Building with OpenGL support.)
} else {
    message(OpenGL support is not available.)
}
```

这就允许了为`release`和`debug`构建定义不同的配置。有关详细信息，请参见[使用作用域](di-7-zhang-qmake-yu-yan.md#\_toc6715)。

以下选项定义了要构建的项目的类型。

<mark style="background-color:yellow;">注：其中一些选项只有在相关平台上使用时才会生效。</mark>

<table><thead><tr><th width="143">选项</th><th>描述</th></tr></thead><tbody><tr><td>qt</td><td>该项目是一个Qt应用程序，应该与Qt库进行链接。您可以使用QT变量来控制应用程序所需的任何其他QT模块。默认情况下会添加此值，但您可以删除它以对非qt项目使用qmake。</td></tr><tr><td>x11</td><td>该项目是一个X11应用程序或库。如果目标使用Qt，则不需要此值。</td></tr></tbody></table>

应用程序和库项目模板为您提供了更专门的配置选项，以微调构建过程。这些选项在构建公共项目类型中都有详细的说明。

例如，如果您的应用程序使用Qt库，并且您希望在调试模式下构建它，那么您的项目文件将包含以下几行：

```
CONFIG += qt debug
```

<mark style="background-color:yellow;">注：您必须使用“+=”，而不是“=”，否则qmake将无法使用Qt的配置来确定项目所需的设置。</mark>

## 声明Qt库 <a href="#_toc17745" id="_toc17745"></a>

如果`CONFIG`变量包含qt值，则会启用qmake对Qt应用程序的支持。这使得微调应用程序使用的哪些Qt模块成为可能。这是通过`QT`变量来实现的，该变量可用于声明所需的扩展模块。例如，我们可以通过以下方式启用`xml`和`network`模块：

```
QT += network xml
```

<mark style="background-color:yellow;">注：默认情况下，QT包括core和gui模块，所以，上面的声明将network模块和xml模块</mark><mark style="background-color:yellow;">**添加**</mark><mark style="background-color:yellow;">到这个默认列表中。以下赋值</mark><mark style="background-color:yellow;">**省略**</mark><mark style="background-color:yellow;">了默认模块，并将在编译应用程序的源代码时导致错误：</mark>

```
QT = network xml  #这将会忽略了core和gui模块
```

如果您想构建一个没有gui模块的项目，那么您需要使用“`-=`”操作符来排除它。默认情况下，QT同时包含`core`和`gui`，所以下面这一行将导致构建一个最小的QT项目：

```
QT -= gui     #只使用了核心模块
```

有关可以添加到`QT`变量中的Qt模块的列表，请参见[QT](../can-kao-shou-ce/di-11-zhang-bian-liang.md)。

## 配置特性 <a href="#_toc23139" id="_toc23139"></a>

可以使用定义在特性`(.prf)`文件中的特性对qmake进行额外的配置。这些额外的特性通常为在构建过程中使用的自定义工具提供支持。若要向构建过程中添加特性，请将特性名称（特性文件名的词干）附加到CONFIG变量中。

例如，qmake可以配置构建过程，以利用[pkg-config](https://www.freedesktop.org/wiki/Software/pkg-config/)支持的外部库，如D-Bus和ogg库，包括以下几行：

```
CONFIG += link_pkgconfig
PKGCONFIG += ogg dbus-1
```

有关添加功能的详细信息，请参见[添加新的特性](di-8-zhang-gao-ji-shi-yong.md#\_toc959)。

## 声明其他库 <a href="#_toc9145" id="_toc9145"></a>

如果除了使用Qt提供的库之外，还在项目中使用其他库，则需要在项目文件中指定它们。

qmake搜索库的路径和要链接的特定库可以添加到LIBS变量中的值列表中。您可以指定到库的路径，或者使用unix样式的符号来指定库和路径。

例如，下面的代码显示了如何指定库：

```
LIBS += -L/usr/local/lib -lmath
```

包含头文件的路径也可以使用类似的方式使用`INCLUDEPATH`变量来指定。

例如，要添加多个要搜索头文件的路径：

```
INCLUDEPATH = c:/msdev/include d:/stl/include
```
