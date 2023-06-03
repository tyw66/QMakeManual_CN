# 第6章 系统平台说明

许多跨平台的项目都可以通过基本的qmake配置特性来处理。然而，在某些平台上，利用特定于平台的特性有时会很有用，甚至是必要的。qmake能识别许多这些特性，这些特性可以通过特定的变量来访问，这些变量只在与它们相关的平台上生效。

## macOS、iOS、tvOS和watchOS <a href="#_toc8557" id="_toc8557"></a>

这些平台特有的特性包括对创建通用二进制文件、框架和捆绑包的支持。

### 源码包和二进制包 <a href="#_toc8455" id="_toc8455"></a>

源码包中提供的qmake版本的配置与二进制包中提供的版本略有不同，因为它使用不同的功能规范。源包通常使用macx-g++规范，而二进制包通常配置为使用macx-xcode规范。

每个包的用户都可以通过使用-spec选项调用qmake来覆盖此配置（有关更多信息，请参阅Running qmake）。例如，要使用二进制程序包中的qmake在项目目录中创建Makefile，请调用以下命令：

### 使用框架 <a href="#_toc5929" id="_toc5929"></a>

qmake能够自动生成构建规则，用于根据框架在标准框架目录上的框架进行链接，位于/Library /Frameworks/。

除标准框架目录以外的目录需要被指定到构建系统中，这是通过将链接器选项添加到LIBS变量来实现的，如下面的示例所示：

LIBS += -F/path/to/framework/directory/

通过将-framework选项和框架的名称附加到LIBS变量来链接到框架本身：

LIBS += -framework TheFramework

### 创建框架 <a href="#_toc26357" id="_toc26357"></a>

TEMPLATE = lib

CONFIG += lib\_bundle

可以配置任何给定的库项目，以便将生成的库文件放置在一个框架中，以便进行部署。为此，将项目设置为使用lib template，并将lib\_bundle选项添加到CONFIG变量中：

使用QMAKE\_BUNDLE\_DATA变量指定与库关联的数据。它保存将与库包一起安装的项，通常用于指定头文件的集合，如下示例所示：

FRAMEWORK\_HEADERS.version = Versions

FRAMEWORK\_HEADERS.files = path/to/header\_one.h path/to/header\_two.h

FRAMEWORK\_HEADERS.path = Headers

QMAKE\_BUNDLE\_DATA += FRAMEWORK\_HEADERS

您可以使用FRAMEWORK\_HEADERS变量来指定特定框架所需的标头。将其附加到QMAKE\_BUNDLE\_DATA变量中，可以确保将有关这些标头的信息添加到将与库包一起安装的资源集合中。此外，框架的名称和版本也由QMAKE\_FRAMEWORK\_BUNDLE\_NAME和QMAKE\_FRAMEWORK\_VERSION变量指定了。默认情况下，这些变量使用的值来自TARGET变量和VERSION变量。

有关部署应用程序和库的更多信息，请参阅QtformacOS- Development。

### 创建和移动Xcode项目 <a href="#_toc4805" id="_toc4805"></a>

macOS上的开发人员可以利用对qmake的Xcode项目文件的支持，如Qt for macOS文档中所述。通过运行qmake从现有的qmake项目文件生成一个Xcode项目。例如：

qmake -spec macx-xcode project.pro

注：如果一个项目的位置稍后在磁盘上移动了，则必须再次运行qmake来处理该项目文件并创建一个新的Xcode项目文件。

### 同时支持两个构建目标 <a href="#_toc22859" id="_toc22859"></a>

目前实现这一方法是不可行的，因为主动构建配置的Xcode概念在概念上不同于构建目标的qmake概念。

Xcode活动构建配置设置用于修改Xcode配置、编译器标志和类似的构建选项。与VisualStudio不同，Xcode不允许根据是选择调试还是发布构建配置来选择特定的库文件。qmake调试和发布设置控制哪些库文件链接到可执行文件。

目前无法从qmake生成的Xcode项目文件中设置Xcode配置设置中的文件。库将以在Xcode构建系统的_Frameworks & Libraries_阶段中链接库的方式被链接。

此外，所选的活动构建配置存储在一个.pbxuser文件中，该文件是由Xcode在第一次加载时生成的，而不是由qmake创建的。

## Windows <a href="#_toc16511" id="_toc16511"></a>

此平台上特定的功能包括对Windows资源文件（提供或自动生成）的支持、创建VisualStudio项目文件，以及在部署使用VisualStudio2005或更高版本开发的Qt应用程序时处理清单文件。

### 添加Windows资源文件 <a href="#_toc776" id="_toc776"></a>

本节介绍如何使用qmake处理Windows资源文件，以使其链接到应用程序可执行文件(EXE)或动态链接库(DLL)。qmake可以选择自动生成一个适当填充的Windows资源文件。

一个链接的Windows资源文件可能包含许多可以由其EXE或DLL访问的元素。但是，Qt资源系统应该用于以平台独立的方式访问链接中的资源。但是链接的Windows资源文件中的一些标准元素可以被Windows本身访问。例如，在Windows资源管理器中，文件属性的版本选项卡将由资源元素填充。此外，还将从这些元素中读取EXE的程序图标。因此，Qt创建的WindowsEXE或DLL最好同时使用这两种技术：通过Qt资源系统链接平台独立的资源，并通过Windows资源文件添加Windows特定的资源。

通常，资源定义脚本(.rc文件)被编译为Windows资源文件。在微软工具链中，RC工具生成一个.res文件，该文件可以与微软链接到EXE或DLL。MinGW工具链使用windres工具生成一个.o文件，该文件可以与MinGW链接器链接到EXE或DLL。

qmake自动生成适当填充的.rc文件是通过设置至少一个系统变量VERSION和RC\_ICONS来触发的。生成的.rc文件会被自动编译和链接。添加到.rc文件中的元素由系统变量QMAKE\_TARGET\_COMPANY、QMAKE\_TARGET\_DESCRIPTION、QMAKE\_TARGET\_COPYRIGHT、QMAKE\_TARGET\_PRODUCT、QMAKE\_MANIFEST、RC\_CODEPAGE、RC\_ICONS、RC\_LANG和VERSION来定义。

如果这些元素还不够，那么qmake就有两个系统变量RC\_FILE和RES\_FILE，它们直接指向外部创建的.rc或.res文件。通过设置其中一个变量，指定的文件将链接到EXE或DLL。

注：如果设置了RC\_FILE或RES\_FILE，则会阻止通过qmake生成.rc文件。在这种情况下，qmake不会对给定的.rc文件或.res或.o文件进行进一步的更改；与.rc文件生成有关的变量没有效果。

### 创建VisualStudio项目文件 <a href="#_toc2480" id="_toc2480"></a>

本节介绍如何将现有的qmake项目导入到VisualStudio中。Qmake能够获取一个项目文件，并创建一个包含开发环境所需的所有必要信息的VisualStudio项目。这是通过将qmake的Project template设置为vcapp（用于应用程序项目）或vclib（用于库项目）来实现的。

这也可以使用命令行选项来设置，例如：

通过输入以下内容，可以递归地在子目录中生成一个.vcproj文件，在主目录中生成一个.sln文件：

每次更新项目文件时，都需要运行qmake来生成更新的VisualStudio项目。

注：如果您使用VisualStudio插件，请选择**Qt > Import from .pro file**以导入.pro文件。

### Visual Studio清单文件 <a href="#_toc29731" id="_toc29731"></a>

在部署使用VisualStudio2005或更高版本构建的Qt应用程序时，请确保在链接应用程序时创建的清单文件得到了正确的处理。对于生成DLL的项目，这是自动处理的。

删除应用程序可执行文件的清单嵌入可以通过对CONFIG变量进行以下分配来完成：

CONFIG -= embed\_manifest\_exe

此外，还可以通过以下对配置变量的分配来删除dll的清单嵌入：

CONFIG -= embed\_manifest\_dll

更多的细节见deployment guide for Windows。
