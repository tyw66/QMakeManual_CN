# 第5章 运行qmake

通过在命令行上指定qmake的各种选项，可以定制其运行时的行为。这些功能允许对构建过程进行微调，提供有用的诊断信息，并可用于为项目指定目标平台。

## 命令语法 <a href="#_toc6828" id="_toc6828"></a>

用于运行qmake的语法采用以下简单的形式：

```
qmake [mode] [options] files
```

## 操作模式 <a href="#_toc3469" id="_toc3469"></a>

qmake支持两种不同的操作模式。在默认模式下，qmake使用项目文件中的信息来生成Makefile，但也可以使用qmake来生成项目文件。如果要显式设置模式，则必须在所有其他选项之前指定它。mode以是以下两个值之一：

* `-makefile` &#x20;

&#x20;      qmake输出将是一个Makefile。

* `-project` &#x20;

&#x20;      qmake输出将是一个项目文件。

<mark style="background-color:yellow;">注：所创建的文件很可能需要进行编辑。例如，添加QT变量以适应项目所需的模块。</mark>

您可以使用`options`指定常规和特定模式的设置。仅适用于生成文件模式的选项将在本节的[Makefile模式选项](di-5-zhang-yun-hang-qmake.md#\_toc22474)部分中描述，而影响项目文件创建的选项将在本节的[项目模式选项](di-5-zhang-yun-hang-qmake.md#\_toc3469)部分中描述。

## 文件 <a href="#_toc12505" id="_toc12505"></a>

`file`参数表示一个或多个项目文件的列表，用空格分隔。

## 一般选项 <a href="#_toc23618" id="_toc23618"></a>

可以在命令行上指定范围广泛的选项来进行qmake，以定制构建过程，并覆盖平台的默认设置。以下基本选项提供了关于使用qmake的帮助，指定qmake写入输出文件的位置，并控制将被写入控制台的调试信息的级别：

* `-help` &#x20;

&#x20;     qmake将详细介绍这些特性，并提供一些有用的帮助。

* `-o file` &#x20;

&#x20;     qmake输出将被定向到file中。如果没有指定此选项，qmake将尝试使用一个合适的输出文件名，这取决于它的运行模式。如果指定了“-”，则输出指向stdout。

* `-d` &#x20;

&#x20;     qmake将输出调试信息。多次添加`-d`会增加冗长性。

用于项目的模板通常由项目文件中的`TEMPLATE`变量指定。您可以通过使用以下选项来覆盖或修改此选项：

* `-t tmpl` &#x20;

&#x20;     qmake将用tmpl覆盖任何设置的`TEMPLATE`变量，但只有在.pro文件被处理_之后_。

* `-tp prefix` &#x20;

&#x20;     qmake将向`TEMPLATE`变量添加`prefix`。

可以调整警告信息的级别，以帮助您在项目文件中发现问题：

* `-Wall` &#x20;

&#x20;     qmake将报告所有已知的警告。

* `-Wnone` &#x20;

&#x20;     qmake不会生成任何警告信息。

* `-Wparser` &#x20;

&#x20;     qmake将只生成解析器警告。这将提醒您在解析项目文件的过程中存在常见的缺陷和潜在的问题。

* `-Wlogic` &#x20;

&#x20;     qmake将警告项目文件中常见的陷阱和潜在的问题。例如，qmake将报告列表中多次出现的文件和丢失的文件。

## “Makefile”模式选项 <a href="#_toc22474" id="_toc22474"></a>

```
qmake -makefile [options] files
```

在Makefile模式下，qmake将生成一个用于构建项目的Makefile。此外，在此模式下，还可以使用以下选项来影响项目文件的生成方式：

* `-after`

&#x20;     qmake将在指定的文件后处理在命令行上给出的分配。

* `-nocache`

&#x20;     qmake将忽略.qmake.cache文件。

* `-nodepend`

&#x20;     qmake将不会生成任何依赖项信息。

* `-cache file`

&#x20;     qmake将使用文件作为缓存文件，而忽略任何其他找到的.qmake.cache文件。

* `-spec spec`

&#x20;     qmake将使用`spec`作为平台和编译器信息的路径，并忽略`QMAKESPEC`的值。

您也可以在命令行上传递qmake分配。它们在指定的所有文件之前被处理。例如，以下命令从test.pro生成Makefile文件：

```
qmake -makefile -o Makefile "CONFIG+=test" test.pro
```

但是，一些指定的选项可以被省略，因为它们是默认值：

```
qmake "CONFIG+=test" test.pro
```

如果您确定希望在指定的文件之后处理变量，则可以传递`-after`选项。当指定此选项时，在`-after`选项之后的命令行上的所有分配都将被推迟，直到解析指定的文件之后。

## “项目”模式选项 <a href="#_toc30995" id="_toc30995"></a>

```
qmake -project [options] files
```

在项目模式下，qmake将生成一个项目文件。此外，您还可以在此模式下提供以下选项：

* `-r`

&#x20;     qmake将递归地查看所提供的目录。

* `-nopwd`

&#x20;     qmake将不会在您当前的工作目录中查找源代码。它将只使用指定的`files`。

在这种模式下，`files`参数可以是文件或目录的列表。如果指定了一个目录，它将包含在`DEPENDPATH`变量中，其中的相关代码将包含在生成的项目文件中。如果给出了一个文件，它将根据其扩展名附加到正确的变量中。例如，ui文件被添加到`FORMS`中，而C++文件被添加到`SOURCES`中。

您也可以在此模式下，在命令行上传递赋值。当这样做时，这些赋值将被最后放在生成的项目文件中。
