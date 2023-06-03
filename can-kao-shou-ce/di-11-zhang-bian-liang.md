# 第11章 变量

qmake的基本行为受到定义每个项目构建过程的变量声明的影响。其中一些声明了每个平台通用的资源，如头文件和源文件。其他的则用于在特定平台上定制编译器和链接器的行为。

特定于平台的变量遵循它们所扩展或修改的变量的命名模式，但在其名称中包含了相关平台的名称。例如，makespec可以使用QMAKE\_LIBS来指定每个项目需要链接的库的列表，而QMAKE\_LIBS\_X11将用于扩展此列表。

ANDROID\_ABI

注：此变量仅适用于Android目标。

指定Android目标ABI。有效值为：armeabi-v7a, arm64-v8a, x86, x86\_64。

ANDROID\_ABIS

注：此变量仅适用于Android目标。

指定Android目标ABI的列表。有效值为：armeabi-v7a, arm64-v8a, x86, x86\_64。

您可以提供ABI作为qmake参数：

qmake ANDROID\_ABIS="armeabi-v7a arm64-v8a"

注：可以在\*.pro文件中使用这个变量，但是不建议使用，因为它将覆盖qmake命令行上指定的任何abi。

ANDROID\_API\_VERSION

注：此变量仅适用于Android目标。

指定AndroidAPI级别编号。有关更多信息，请参见Android Build Numbers。

ANDROID\_BUNDLED\_JAR\_DEPENDENCIES

注：此变量仅适用于Android模块。

这在编写Qt模块时很有用。它指定了模块以.jar格式使用的预绑定依赖项的列表，例如：

ANDROID\_BUNDLED\_JAR\_DEPENDENCIES += jar/QtAndroid.jar

ANDROID\_DEPLOYMENT\_DEPENDENCIES

注：此变量仅适用于Android目标。

默认情况下，androiddeployqt将检测应用程序的依赖关系。但是，由于无法检测到插件的运行时使用，因此可能会出现失误，因为您的应用程序可能依赖于任何具有潜在依赖性的插件。如果您想最小化APK的大小，则可以使用此变量覆盖自动检测。这应该包含需要包含的所有Qt文件的列表，其中包含相对于Qt安装根目录的路径。

注：只包含使用此变量指定的Qt文件。未能包含所有正确的文件可能会导致崩溃。确保文件以正确的加载顺序列出也很重要。这个变量提供了一种完全覆盖自动检测的方法，因此，如果一个库在其依赖项之前列出，它将无法在某些设备上加载。

ANDROID\_DEPLOYMENT\_SETTINGS\_FILE

注：此变量仅适用于Android目标。

指定部署和部署所需的android-deployment-settings.json文件的路径。这将覆盖由qmake生成的设置文件的路径，因此您必须确保提供一个有效的设置文件。

ANDROID\_EXTRA\_LIBS

注：此变量仅适用于Android目标。

将复制到应用程序的libs文件夹并在启动时加载的外部库列表。例如，这可以用于在应用程序中启用OpenSSL。有关详细信息，请参见Adding OpenSSL Support for Android.。

要包含多个abi的外部库，其中每个abi都有自己的目录，请使用以下方法：

for (abi, ANDROID\_ABIS): ANDROID\_EXTRA\_LIBS += \$$PWD/\$${abi}/library\_name.so

否则，如果ABI包含在库的名称中，请使用以下内容：

for (abi, ANDROID\_ABIS): ANDROID\_EXTRA\_LIBS += \$$PWD/library\_name\_\$${abi}.so

ANDROID\_EXTRA\_PLUGINS

注：此变量仅适用于Android目标。

指定C++插件或资源的路径，您的应用程序必须打包，但不能通过资产系统传递，例如QML插件。有了这个变量，androiddeployqt将确保所有内容都正确打包和部署。

ANDROID\_EXTRA\_PLUGINS必须指向构建额外插件的目录。此外，构建目录结构必须遵循类似于Qt插件的命名约定，即_插件/<插件名称>_。

插件库的名称格式应该为_libplugins\_\<type>\_\<name>\_\<abi>.so_。为了实现这一点，插件配置文件可以定义如下：

TEMPLATE = lib

CONFIG += plugin

PLUGIN\_TYPE = imageformats

DESTDIR = \$$top\_builddir/plugins/myplugin

TARGET = \$$qt5LibraryTarget(myplugin, "plugins/\$$PLUGIN\_TYPE/")

使用top\_builddir在.qmake.conf中定义为：

top\_builddir=\$$shadowed(\$$PWD)

这将确保对插件库_(plugins/myplugin/libplugins\_imageformats\_myplugin\_armeabi-v7a.so)_.应用正确的名称manning

然后，假设一个额外的图像格式插件_myplugin_被构建为_\$$DESTDIR/plugins/myplugin/,_，以下内容确保了它会被正确地打包：

ANDROID\_EXTRA\_PLUGINS += \$$top\_builddir/plugins

ANDROID\_FEATURES

注：此变量仅适用于Android模块。

指定一个模块的功能列表：

ANDROID\_FEATURES += android.hardware.location.gps

[有关更多信息，请参见Android \<uses-feature> Docs。](https://developer.android.com/guide/topics/manifest/uses-feature-element)

ANDROID\_LIB\_DEPENDENCIES

注：此变量仅适用于Android模块。

这在编写Qt模块时很有用。它指定了模块使用的预构建依赖项的列表，例如：

ANDROID\_LIB\_DEPENDENCIES += \\

plugins/libplugins\_platforms\_qtforandroid.so

ANDROID\_MIN\_SDK\_VERSION

注：此变量仅适用于Android目标。

指定项目的最小AndroidAPI级别。默认情况下，此变量被设置为API级别21。

ANDROID\_PACKAGE\_SOURCE\_DIR

注：此变量仅适用于Android目标。

指定自定义Android软件包模板的路径。Android软件包模板包含：

* AndroidManifest.xml文件
* build.gradle文件和其他Gradle脚本
* res/values/libs.xml文件

此变量指定的路径可以包含src目录下的自定义Java类。默认情况下，安卓部署qt工具将应用程序模板从forAndroid安装路径的Qt复制到项目的构建目录中，然后在此目录上复制此变量指定的路径内容，覆盖任何现有文件。例如，您可以为应用程序创建一个自定义AndroidManifest.xml，然后将其直接放到此变量指定的目录中。

ANDROID\_PERMISSIONS

注：此变量仅适用于Android模块。

指定一个模块的权限列表：

ANDROID\_PERMISSIONS += android.permission.ACCESS\_FINE\_LOCATION

[有关更多信息，请参见Android \<uses-permission> Docs.。](https://developer.android.com/guide/topics/manifest/uses-permission-element)

ANDROID\_TARGET\_SDK\_VERSION

注：此变量仅适用于Android目标。

指定项目的目标Android API级别。默认情况下，此变量被设置为API级别30。

ANDROID\_VERSION\_CODE

注：此变量仅适用于Android目标。

指定应用程序的版本号。有关更多信息，请参阅Android App Versioning。

ANDROID\_VERSION\_NAME

注：此变量仅适用于Android目标。

指定中的应用程序的版本为人类可读的字符串。有关更多信息，请参阅Android App Versioning。

CONFIG

指定项目配置和编译器选项。这些值可以被qmake在内部识别，并具有特殊的意义。

注：这些值是区分大小写的。

以下CONFIG值可控制编译器和链接器标志：

| **选项**                                    | **描述**                                                                                                                                                                 |
| ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| release                                   | 该项目将以发布模式进行构建。如果还指定了debug，则最后一个将生效。                                                                                                                                    |
| debug                                     | 该项目会在调试模式下构建。                                                                                                                                                          |
| debug\_and\_release                       | 该项目会同时在调试和发布模式下构建。                                                                                                                                                     |
| debug\_and\_release\_target               | 此选项会被默认设置。如果还设置了debug\_and\_release，那么调试和发布构建最终会出现在单独的调试和发布目录中。                                                                                                        |
| build\_all                                | 如果指定了debug\_and\_release，则默认以调试和发布模式构建项目。                                                                                                                              |
| autogen\_precompile\_source               | 自动生成一个.cpp文件，其中包含在.pro文件中指定的预编译头文件。                                                                                                                                    |
| ordered                                   | <p>当使用subdirs模板时，此选项指定所列出的目录应按其给出目录的顺序进行处理。</p><p>注：不鼓励使用此选项。参看SUBDIRS变量中所述的指定依赖项。</p>                                                                                 |
| precompile\_header                        | 支持在项目中使用预编译的标头。                                                                                                                                                        |
| <p>precompile_header_c</p><p>(仅限MSVC)</p> | 支持对C文件使用预编译的头文件。                                                                                                                                                       |
| warn\_on                                  | 编译器应该输出尽可能多的警告。如果也指定了warn\_off，则最后一个warn\_off将生效。                                                                                                                      |
| warn\_off                                 | 编译器应该输出尽可能少的警告。                                                                                                                                                        |
| exceptions                                | 启用异常支持。默认已设置。                                                                                                                                                          |
| exceptions\_off                           | 禁用异常支持。                                                                                                                                                                |
| ltcg                                      | 启用链接时间代码生成。此选项在默认情况下是关闭的。                                                                                                                                              |
| rtti                                      | 启用RTTI支持。默认情况下，将使用编译器的默认值。                                                                                                                                             |
| rtti\_off                                 | 禁用RTTI支持。默认情况下，将使用编译器的默认值。                                                                                                                                             |
| stl                                       | 启用STL支持。默认情况下，将使用编译器的默认值。                                                                                                                                              |
| stl\_off                                  | 禁用STL支持。默认情况下，将使用编译器的默认值。                                                                                                                                              |
| thread                                    | 已启用线程支持。默认情况下，当CONFIG包含qt时启用。                                                                                                                                          |
| utf8\_source                              | 指定项目的源文件使用UTF-8编码。默认情况下，将使用编译器的默认值。                                                                                                                                    |
| hide\_symbols                             | 将二进制文件中符号的默认可见性设置为隐藏。默认情况下，将使用编译器的默认值。                                                                                                                                 |
| c99                                       | 启用C99支持。如果编译器不支持C99，或不能选择C标准，则此选项无效。默认情况下，将使用编译器的默认值。                                                                                                                  |
| c11                                       | 启用C11支持。如果编译器不支持C11，或不能选择C标准，则此选项无效。默认情况下，将使用编译器的默认值。                                                                                                                  |
| c17                                       | 启用C17（也被称为C18）支持。如果编译器不支持C17，或不能选择C标准，则此选项无效。默认情况下，将使用编译器的默认值。                                                                                                         |
| c18                                       | 这是c17值的别名。                                                                                                                                                             |
| strict\_c                                 | 禁用对C编译器扩展的支持。默认情况下，它们是启用的。                                                                                                                                             |
| c++11                                     | 启用C++11支持。如果编译器不支持C++11，或不能选择C++标准，则此选项无效。默认情况下，支持已启用。                                                                                                                 |
| c++14                                     | 启用C++14支持。如果编译器不支持C++14，或不能选择C++标准，则此选项无效。默认情况下，将使用编译器的默认值。                                                                                                            |
| c++1z                                     | 启用C++17支持。如果编译器不支持C++17，或不能选择C++标准，则此选项无效。默认情况下，支持已禁用。                                                                                                                 |
| c++17                                     | 与c++1z相同。                                                                                                                                                              |
| c++2a                                     | 启用C++2a支持。如果编译器不支持C++2a，或不能选择C++标准，则此选项无效。默认情况下，支持已禁用。                                                                                                                 |
| c++latest                                 | 启用对编译器支持的最新C++语言标准的支持。默认情况下，此选项被禁用。                                                                                                                                    |
| strict\_c++                               | 禁用对C++编译器扩展的支持。默认情况下，它们是启用的。                                                                                                                                           |
| depend\_includepath                       | 将INCLUDEPATH值附加到DEPENDPATH。默认已设置。                                                                                                                                      |
| lrelease                                  | 为在TRANSLATIONS和EXTRA\_TRANSLATIONS中列出的所有文件运行lrelease。如果未设置embed\_translations，请将生成的.qm文件安装到QM\_FILES\_INSTALL\_PATH中。使用QMAKE\_LRELEASE\_FLAGS向lrelease调用添加选项。默认情况下未设置。 |
| embed\_translations                       | 将从lrelease中生成的翻译嵌入到可执行文件中的QM\_FILES\_RESOURCE\_PREFIX下。也需要设置lrelease。默认情况下未设置。                                                                                         |
| create\_libtool                           | 为当前构建的库创建一个libtool.la文件。                                                                                                                                               |
| create\_pc                                | 为当前构建的库创建一个pkg-config.pc文件。                                                                                                                                            |
| no\_batch                                 | 仅限NMake：关闭NMake批处理规则或推理规则的生成。                                                                                                                                          |
| skip\_target\_version\_ext                | 取消Windows上DLL文件名的自动版本号。                                                                                                                                                |
| suppress\_vcproj\_warnings                | 抑制VS项目生成器的警告。                                                                                                                                                          |
| windeployqt                               | 在链接后自动调用windeployqt，并将输出添加为部署项。                                                                                                                                        |
| dont\_recurse                             | 抑制当前子项目的qmake递归。                                                                                                                                                       |
| no\_include\_pwd                          | 不要将当前目录添加到INCLUDEPATH中。                                                                                                                                                |
| compile\_included\_sources                | 默认情况下，qmake不编译其他源文件中包含的源文件。此选项将禁用此行为。                                                                                                                                  |

当您使用debug\_and\_release选项（这是Windows下的默认选项）时，项目将被处理三次：一次生成元Makefile，还有两次生成Makefile.Debug和Makefile.Release

在后一个过程中，build\_pass和相应的debug或release选项被附加到CONFIG中。这使得执行特定于构建的任务成为可能。例如：

build\_pass:CONFIG(debug, debug|release) {

unix: TARGET = \$$join(TARGET,,,\_debug)

else: TARGET = \$$join(TARGET,,,d)

}

作为手动编写构建类型条件的替代方法，一些变量提供了特定于构建的变体，例如除了一般的QMAKE\_LFLAGS之外的QMAKE\_LFLAGS\_RELEASE。这些应该在可用时使用。

元Makefile可以通过debug和release目标来调用子构建，并通过all目标进行组合构建。当使用build\_all配置选项时，组合构建是默认版本。否则，来自集(debug、release)的最后一个指定的配置选项将确定默认值。在这种情况下，您可以显式地调用所有目标来同时构建这两种配置：

注：在生成VisualStudio和Xcode项目时，详细信息略有不同。

当链接一个库时，qmake依赖于底层平台来知道这个库链接的是哪些其他库。但是，如果是静态链接，qmake将不会得到此信息，除非我们使用以下配置选项：

| **选项**           | **描述**                                                                     |
| ---------------- | -------------------------------------------------------------------------- |
| create\_prl      | 此选项使qmake能够跟踪这些依赖项。启用此选项后，qmake将创建一个扩展名为.prl的文件，该文件将保存有关库的元信息（更多信息请参阅库依赖）。 |
| link\_prl        | 当启用此选项时，qmake将处理由应用程序链接到的所有库，并找到它们的元信息（更多信息请参阅库依赖）。                        |
| no\_install\_prl | 此选项禁用为生成的.prl文件生成安装规则。                                                     |

注：在构建静态库时需要使用create\_prl选项，而在使用静态库时则需要使用link\_prl选项。

以下选项定义了应用程序或应用程序库类型：

| **选项**              | **描述**                                                                                                                                                                       |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| qt                  | 目标是一个Qt应用程序或库，并且需要Qt库和头文件。相应的Qt头文件包含和库路径将自动添加到项目中。这是在默认情况下定义的，并且可以使用\l{#qt}{QT}变量进行微调。                                                                                       |
| x11                 | 目标是一个X11应用程序或库。相应的路径和库包含将自动添加到项目中。                                                                                                                                           |
| testcase            | 目标是一个自动测试。一个检查目标将被添加到生成的Makefile中，以运行该测试。仅在生成生成文件时相关。                                                                                                                        |
| insignificant\_test | 自动测试的退出代码将被忽略。只有在testcase也被设置时才相关。                                                                                                                                           |
| windows             | 目标是一个Win32窗口应用程序（仅限应用程序）。相应的包含路径、编译器标志和库将自动添加到项目中。                                                                                                                           |
| console             | 目标是一个Win32控制台应用程序（仅限应用程序）。相应的包含路径、编译器标志和库将自动添加到项目中。考虑对跨平台应用程序使用选项cmdline。                                                                                                    |
| cmdline             | 目标是一个跨平台的命令行应用程序。在Windows上，这意味着CONFIG=console。在macOS上，这意味着CONFIG-=app\_bundle。                                                                                               |
| shared              | 该目标是一个共享的对象/DLL。相应的包含路径、编译器标志和库将自动添加到项目中。请注意，dll也可以在所有平台上使用；将创建一个后缀适合目标平台(.dll或.so)的共享库文件。                                                                                   |
| dll                 |                                                                                                                                                                              |
| static              | 目标是一个静态库（仅限库）。相应的编译器标志将被自动添加到项目中。                                                                                                                                            |
| staticlib           |                                                                                                                                                                              |
| plugin              | 目标是一个插件（仅限库）。这也支持dll。                                                                                                                                                        |
| designer            | 目标是Qt设计师的一个插件。                                                                                                                                                               |
| no\_lflags\_merge   | 确保在使用LIBS变量中存储的库列表之前不会简化为唯一值的列表。                                                                                                                                             |
| metatypes           | 为当前项目创建一个\<name>\_metatypes.json文件。\<name>是TARGET的全小写基名。                                                                                                                     |
| qmltypes            | 自动注册在C++中定义的QML类型。有关更多信息，请参见Defining QML Types from C++.。另外，请为当前项目创建一个\<template>.qmltypes文件。如果设置了plugin，\<template>将是plugins（复数，由于历史原因），否则将是TEMPLATE的值。qmltype意味着metatypes。 |

这些选项仅在Windows上定义特定的特性：

| **选项**               | **描述**                                                                                   |
| -------------------- | ---------------------------------------------------------------------------------------- |
| flat                 | 当使用vcapp模板时，这将把所有的源文件放到源组中，并把头文件放到头组中，而不管它们位于哪个目录中。关闭此选项将根据文件所在的目录对源/头组中的文件进行分组。这是默认打开的。 |
| embed\_manifest\_dll | 在作为库项目的一部分创建的DLL中嵌入一个清单文件。                                                               |
| embed\_manifest\_exe | 在作为应用程序项目的一部分创建的EXE中嵌入一个清单文件。                                                            |

有关嵌入清单文件的选项的详细信息，请参阅系统平台说明。

以下选项仅对macOS生效：

| **选项**         | **描述**                       |
| -------------- | ---------------------------- |
| app\_bundle    | 将可执行文件放到一个包中（这是默认值）。         |
| lib\_bundle    | 将该库放入一个库包中。                  |
| plugin\_bundle | 将插件放到一个插件包中。Xcode项目生成器不支持此值。 |

捆绑包的构建过程也会受到QMAKE\_BUNDLE\_DATA变量的内容的影响。

以下选项仅在Linux/Unix平台上生效：

| **选件**                | **描述**           |
| --------------------- | ---------------- |
| largefile             | 包括对大文件的支持。       |
| separate\_debug\_info | 将库的调试信息放在单独的文件中。 |

在解析作用域范围时，也将检查CONFIG变量。您可以为这个变量分配任何东西。

例如：

CONFIG += console newstuff

...

newstuff {

SOURCES += new.cpp

HEADERS += new.h

}

DEFINES

qmake将此变量的值添加为C编译器的预处理器宏(-D选项)。

例如：

DEFINES\_DEBUG

指定针对调试配置的预处理器定义。此变量的值添加到DEFINES中。这个变量通常在qmake.conf中设置，很少需要修改。

该变量在Qt5.13.2中引入。

DEFINES\_RELEASE

指定针对发布配置的预处理器定义。此变量的值添加到DEFINES中。这个变量通常在qmake.conf中设置，很少需要修改。

注：对于MSVC mkspecs，该变量在默认情况下包含值NDEBUG。

该变量在Qt5.13.2中引入。

DEF\_FILE

注：此变量在使用app模板时仅在Windows上使用。

指定要包含在项目中的一个.def文件。

DEPENDPATH

指定一个目录列表，让qmake用来扫描、解析依赖关系。当qmake经过源代码中#include的头文件时，将使用此变量。

DESTDIR

指定要放置目标(target)文件的位置。

例如：

注：受支持的字符列表可能取决于所使用的构建工具。特别是，括号与make不起作用。

DISTFILES

指定要包含在dist target中的文件的列表。仅限UnixMake的spec文件支持此特性。

例如：

DISTFILES += ../program.txt

译注：dist target是make中的概念

DLLDESTDIR

注：此变量仅适用于Windows目标。

指定复制target dll的位置。

EXTRA\_TRANSLATIONS

指定包含将用户界面文本翻译为非本地语言的翻译(.ts)文件列表。

与TRANSLATIONS相比，EXTRA\_TRANSLATIONS中的翻译文件只能通过lrelease处理，而不是lupdate处理。

您可以使用CONFIG+=lrelease在构建过程中自动编译文件，并CONFIG+=lrelease embed\_translations使它们在Qt资源系统中可用。

有关Qt的国际化(i18n)和本地化(l10n)的更多信息，请参阅Qt语言家手册。

FORMS

指定要在编译前由uic处理的UI文件(请参见Qt设计师手册)。构建这些UI文件所需的所有依赖项、头文件和源文件都将自动添加到项目中。

例如：

FORMS = mydialog.ui \\

mywidget.ui \\

myconfig.ui

GUID

指定在.vcproj文件中设置的GUID。GUID通常是随机确定的。但是，如果您需要一个固定的GUID，它可以使用这个变量进行设置。

此变量仅特定于.vcproj文件；否则将忽略它。

HEADERS

定义项目的头文件。

qmake会自动检测报头中的类是否需要moc，并将适当的依赖项和文件添加到项目中，以生成和链接moc文件。

例如：

HEADERS = myclass.h \\

login.h \\

mainwindow.h

另请参见SOURCES。

ICON

此变量仅在Mac OS上用于设置应用程序图标。有关更多信息，请参见the application icon documentation。

IDLSOURCES

此变量仅在Windows上用于Visual Studio项目生成，以便将指定的文件放在“生成的文件”文件夹中。

INCLUDEPATH

指定在编译项目时应该搜索的#include目录。

例如：

INCLUDEPATH = c:/msdev/include d:/stl/include

若要指定包含空格的路径，请使用Whitespace中描述的技术引用该路径。

win32:INCLUDEPATH += "C:/mylibs/extra headers"

unix:INCLUDEPATH += "/home/user/extra headers"

INSTALLS

指定在执行make install或类似安装过程时将安装的资源列表。列表中的每个项目通常都使用属性定义，这些属性提供有关它将安装位置的信息。

例如，以下target.path定义描述了将安装生成目标的位置，并且INSTALLS任务会将生成目标添加到要安装的现有资源的列表中：

target.path += \$$\[QT\_INSTALL\_PLUGINS]/imageformats

INSTALLS += target

INSTALLS有一个.config成员，可以接受多个值：

| **值**                | **描述**                                                                                                   |
| -------------------- | -------------------------------------------------------------------------------------------------------- |
| no\_check\_exist     | 如果没有设置，qmake将查看要安装的文件是否确实存在，如果这些文件不存在，则qmake将不创建安装规则。如果您需要安装作为构建过程的一部分生成的文件，比如由qdoc创建的HTML文件，请使用此配置值。    |
| nostrip              | 如果设置，典型的Unix的strip功能将被关闭，调试信息将保留在二进制文件中。                                                                 |
| executable           | 在Unix上，这将设置可执行标志。                                                                                        |
| no\_build            | 当您进行make install时，并且还没有项目的构建时，项目首先构建，然后安装。如果您不希望出现此行为，请设置此配置值，以确保生成目标不会作为对安装目标的依赖项添加。                    |
| no\_default\_install | 项目有一个顶级项目目标，当您进行make install时，所有项目都安装了。但是，如果您有一个设置了此配置值的安装目标，则默认情况下不会安装它。然后你必须明确地说make install\_\<file>。 |

有关更多信息，请参见安装文件。

此变量还用于指定将部署到嵌入式设备上的附加文件。

JAVA\_HOME

注：此变量仅对安卓目标有用。

指定用于构建项目的JDK/OpenJDK安装路径。

LEXIMPLS

指定Lex实现文件的列表。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

LEXOBJECTS

指定中间Lex对象文件的名称。这个变量的值通常由qmake处理，很少需要修改。

LEXSOURCES

指定Lex源文件的列表。所有依赖项、头和源文件将自动添加到项目中，用于构建这些lex文件。

例如：

LIBS

指定要链接到项目中的库的列表。如果使用Unix的-l（库）和-L（库路径）标志，qmake将在Windows上正确地处理库（即，将库的完整路径传递给链接器）。qmake必须存在该库，才能找到-l库所在的目录。

例如：

unix:LIBS += -L/usr/local/lib -lmath

win32:LIBS += c:/mylibs/math.lib

若要指定包含空格的路径，请使用空格章节中描述的技术引用该路径。

win32:LIBS += "C:/mylibs/extra libs/extra.lib"

unix:LIBS += "-L/home/user/extra libs" -lextra

默认情况下，存储在LIBS中的库的列表在使用之前会简化为唯一名称的列表。要更改此行为，请向配置变量中添加no\_lflags\_merge选项：

CONFIG += no\_lflags\_merge

LIBS\_PRIVATE

指定要单独链接到项目中的库的列表。这个变量的行为与LIBS相同，只是为Unix构建的共享库项目不会在它们的链接接口中公开这些依赖关系。

这样做的效果是，如果项目C依赖于库B，而库B私下依赖于库A，但C也想直接使用从A中获得的符号，那么它需要显式地链接到A。换句话说，私有链接的库在构建时不会传递地公开。

LITERAL\_HASH

当变量声明中需要文字哈希字符（#）时，将使用此变量，可能作为文件名的一部分或传递给某些外部应用程序的字符串。

例如：

\# To include a literal hash character, use the \$$LITERAL\_HASH variable:

urlPieces = http://doc.qt.io/qt-5/qtextdocument.html pageCount

message(\$$join(urlPieces, \$$LITERAL\_HASH))

通过以这种方式使用LITERAL\_HASH，可以使用#字符来构造一个message()函数打印到控制台使用的URL。

MAKEFILE

指定所生成的Makefile的名称。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

MAKEFILE\_GENERATOR

指定生成Makefile生成器的名称。这个变量的值通常由qmake在内部处理，很少需要修改。

MSVCPROJ\_\*

这些变量由qmake在内部处理，不应该被修改或使用。

MOC\_DIR

指定所有中间moc文件的目录。

例如：

unix:MOC\_DIR = ../myproject/tmp

win32:MOC\_DIR = c:/myproject/tmp

OBJECTIVE\_HEADERS

定义项目的Objective-C++头文件。

qmake会自动检测头文件中的类是否需要moc，并将适当的依赖项和文件添加到项目中，以生成和链接moc文件。

这类似于HEADERS变量，但将允许生成的moc文件用Objective-C++编译器进行编译。

另请参见OBJECTIVE\_SOURCES。

OBJECTIVE\_SOURCES

指定项目中所有Objective-C++源文件的名称。

这个变量现在已经过时，Objective-C/C++文件(.m和.mm)可以将添加到SOURCES变量中。

另请参见OBJECTIVE\_HEADERS。

OBJECTS

该变量将从SOURCES变量中自动填充。每个源文件的扩展名将被.o(Unix)或.obj(Win32)取代。您可以向该列表中添加对象。

OBJECTS\_DIR

指定应放置所有中间对象的目录。

例如：

unix:OBJECTS\_DIR = ../myproject/tmp

win32:OBJECTS\_DIR = c:/myproject/tmp

POST\_TARGETDEPS

列出TARGET所依赖的库。一些后端，例如Visual Studio和Xcode项目文件的生成器，不支持此变量。通常，这些构建工具在内部支持这个变量，它对于显式地列出依赖的静态库非常有用。

此列表被放置在所有内置（和\$$PRE\_TARGETDEPS）依赖项之后。

PRE\_TARGETDEPS

列出目标所依赖的库。一些后端，例如VisualStudio和Xcode项目文件的生成器，不支持此变量。通常，这些构建工具在内部支持这个变量，它对于显式地列出依赖的静态库非常有用。

此列表被放置在所有内置依赖项之前。

PRECOMPILED\_HEADER

指示用于创建预编译头文件的头文件，以提高项目的编译速度。预编译头目前只在某些平台上受支持（所有MSVC项目类型、Apple-Xcode、Makefile、Unix-gcc3.3及以上版本）。

PWD

指向包含正在解析的当前文件的目录的完整路径。这对于在编写项目文件以支持影子构建时，引用源码目录树中的文件非常有用。

另请参见\_PRO\_FILE\_PWD\_。

注：不要试图覆盖此变量的值。

OUT\_PWD

指向qmake放置生成的Makefile的目录的完整路径。

注：不要试图覆盖此变量的值。

QM\_FILES\_RESOURCE\_PREFIX

指定资源系统中可通过CONFIG+=embed\_translations提供.qm文件的目录。

默认值为：/i18n/。

QM\_FILES\_INSTALL\_PATH

指定目标目录。由CONFIG+=lrelease生成的qm文件将被安装到此。如果设置了CONFIG+=embed\_translations，则没有任何效果。

QML\_IMPORT\_PATH

此变量仅由Qt Creator使用。详情请参见Qt Creator: Using QML Modules with Plugins。

QMAKE

指定qmake程序本身的名称，并放置在生成的make文件中。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKESPEC

一个系统变量，它包含在生成make文件时使用的qmake配置的完整路径。此变量的值将被自动计算出来。

注：不要试图覆盖此变量的值。

QMAKE\_AR\_CMD

此变量仅在Unix平台上使用。

指定在创建共享库时要执行的命令。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_BUNDLE\_DATA

注：此变量仅用于macOS、iOS、tvOS和watchOS。

指定将与库包一起安装的数据，并且通常用于指定头文件的集合。

例如，以下行将path/to/header\_one.h和path/to/header\_two.h添加到包含框架提供的头信息的组：

FRAMEWORK\_HEADERS.version = Versions

FRAMEWORK\_HEADERS.files = path/to/header\_one.h path/to/header\_two.h

FRAMEWORK\_HEADERS.path = Headers

QMAKE\_BUNDLE\_DATA += FRAMEWORK\_HEADERS

最后一行将有关标题的信息添加到将与库包一起安装的资源集合中。

当将lib\_bundle选项添加到CONFIG变量时，就会创建库包。

有关创建库包的更多信息，请参阅系统平台说明。

QMAKE\_BUNDLE\_EXTENSION

注：此变量仅用于macOS、iOS、tvOS和watchOS。

指定要用于库包的扩展名。这允许使用自定义扩展创建框架，而不是标准的框架目录扩展名创建框架。

例如，以下定义将生成一个具有.myframework扩展的框架：

QMAKE\_BUNDLE\_EXTENSION = .myframework

QMAKE\_CC

指定在构建包含C源代码的项目时将使用的C编译器。在处理编译器的Make文件时，只要它包含在PATH上，就只需要指定编译器可执行文件中的文件名。

QMAKE\_CFLAGS

指定用于构建项目的C编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。特定于调试和发布模式的标志可以通过分别通过修改QMAKE\_CFLAGS\_DEBUG和QMAKE\_CFLAGS\_RELEASE变量来调整。

QMAKE\_CFLAGS\_DEBUG

指定调试构建的C编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CFLAGS\_RELEASE

指定发布版本的C编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CFLAGS\_RELEASE\_WITH\_DEBUGINFO

为在配置中设置force\_debug\_info的发布版本构建指定C编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CFLAGS\_SHLIB

注：此变量仅在Unix平台上使用。

指定用于创建共享库的编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CFLAGS\_THREAD

指定用于创建多线程应用程序的编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CFLAGS\_WARN\_OFF

此变量仅在设置了warn\_off配置选项时使用。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CFLAGS\_WARN\_ON

此变量仅在设置了warn\_on配置选项时使用。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CLEAN

指定要通过make clean来删除的文件列表，包括生成的文件（例如通过moc和uic）和object文件。

QMAKE\_CXX

指定在构建包含C++源代码的项目时将使用的C++编译器。在处理编译器的Make文件时，只要它的路径包含在PATH变量上，就只需要指定编译器可执行文件中的文件名。

QMAKE\_CXXFLAGS

指定用于构建项目的C++编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。特定于调试和发布模式的标志可以通过分别通过修改QMAKE\_CXXFLAGS\_DEBUG和QMAKE\_CXXFLAGS\_RELEASE变量来调整。

QMAKE\_CXXFLAGS\_DEBUG

为调试构建的C++编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CXXFLAGS\_RELEASE

为发布版本的C++编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CXXFLAGS\_RELEASE\_WITH\_DEBUGINFO

为在CONFIG中设置force\_debug\_info的发布版本构建指定C++编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CXXFLAGS\_SHLIB

指定用于创建共享库的C++编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CXXFLAGS\_THREAD

指定用于创建多线程应用程序的C++编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CXXFLAGS\_WARN\_OFF

指定用于抑制编译器警告的C++编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_CXXFLAGS\_WARN\_ON

指定用于生成编译器警告的C++编译器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_DEVELOPMENT\_TEAM

注：此变量仅用于macOS、iOS、tvOS和watchOS。

用于对证书和预设配置文件进行签名的开发团队的标识符。

QMAKE\_DISTCLEAN

指定要通过make distclean进行清理来删除的文件的列表。

QMAKE\_EXTENSION\_SHLIB

包含针对共享库的扩展名。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

注：更改扩展名的特定于平台的变量将覆盖此变量的内容。

QMAKE\_EXTENSION\_STATICLIB

包含针对共享静态库的扩展名。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_EXT\_MOC

包含在所包含的moc文件上使用的扩展名。

另请参见文件扩展。

QMAKE\_EXT\_UI

包含在Qt设计师的UI文件上使用的扩展名。

另请参见文件扩展。

QMAKE\_EXT\_PRL

包含用于已创建的PRL文件的扩展名。

另请参见文件扩展、库依赖关系。

QMAKE\_EXT\_LEX

包含在提供给Lex的文件上使用的扩展名。

另请参见文件扩展，LEXSOURCES。

QMAKE\_EXT\_YACC

包含在提供给Yacc的文件上使用的扩展名。

另请参见文件扩展，YACCSOURCES。

QMAKE\_EXT\_OBJ

包含用于已生成的对象文件的扩展名。

另请参见文件扩展。

QMAKE\_EXT\_CPP

包含应该被解释为C-++源代码的文件的后缀。

另请参见文件扩展。

QMAKE\_EXT\_H

包含应该被解释为C头文件的文件的后缀。

另请参见文件扩展。

QMAKE\_EXTRA\_COMPILERS

指定其他编译器或预处理器的列表。

另请参见添加编译器。

QMAKE\_EXTRA\_TARGETS

指定其他qmake目标的列表。

另请参见添加自定义目标。

QMAKE\_FAILED\_REQUIREMENTS

包含出现失败的需求的列表。此变量的值由qmake设置，不能进行修改。

另请参见requires()和REQUIRES。

QMAKE\_FRAMEWORK\_BUNDLE\_NAME

注：此变量仅用于macOS、iOS、tvOS和watchOS。

在框架项目中，此变量包含要用于所构建的框架的名称。

默认情况下，此变量包含与TARGET变量相同的值。

有关创建框架和库包的更多信息，请参见创建框架。

QMAKE\_FRAMEWORK\_VERSION

注：此变量仅用于macOS、iOS、tvOS和watchOS。

对于构建目标是macOS、iOS、tvOS或watchOS框架的项目，此变量用于指定将应用于所构建的框架的版本号。

默认情况下，此变量包含与VERSION变量相同的值。

有关创建框架的更多信息，请参见创建框架。

QMAKE\_HOST

提供有关运行qmake的主机的信息。例如，您可以从QMAKE\_HOST.arch中检索主机体系结构。

| **键**            | **值**       |
| ---------------- | ----------- |
| .arch            | 主机架构        |
| .os              | 主机操作系统      |
| .cpu\_count      | 可用CPU数量     |
| .name            | 主机名         |
| .version         | 主机操作系统版本号   |
| .version\_string | 主机操作系统版本字符串 |

win32-g++:contains(QMAKE\_HOST.arch, x86\_64):{

message("Host is 64bit")

...

}

QMAKE\_INCDIR

指定附加到INCLUDEPATH中的系统头路径的列表。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_INCDIR\_EGL

指定在构建一个支持OpenGL/ES或OpenVG的目标时，要添加到INCLUDEPATH中的EGL头文件的位置。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_INCDIR\_OPENGL

指定在构建具有OpenGL支持的目标时，要添加到INCLUDEPATH的OpenGL头文件的位置。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

如果OpenGL实现使用EGL（大多数OpenGL/ES系统），那么可能还需要设置QMAKE\_INCDIR\_EGL。

QMAKE\_INCDIR\_OPENGL\_ES2

此变量指定在构建一个支持OpenGLES2的目标时，要添加到INCLUDEPATH中的OpenGL头文件的位置。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

如果OpenGL实现使用EGL（大多数OpenGL/ES系统），那么可能还需要设置QMAKE\_INCDIR\_EGL。

QMAKE\_INCDIR\_OPENVG

指定在构建具有OpenVG支持的目标时，要添加到包含路径中的OpenVG头文件的位置。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

如果OpenVG实现使用EGL，那么可能还需要设置QMAKE\_INCDIR\_EGL。

QMAKE\_INCDIR\_X11

注：此变量仅在Unix平台上使用。

指定在构建X11目标时要添加到包含路径的X11头文件路径的位置。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_INFO\_PLIST

注：此变量仅用于macOS、iOS、tvOS和watchOS平台。

指定您希望在macOS、iOS、tvOS和watchOS应用程序包中包含的属性列表文件.plist的名称。

在.plist文件中，您可以定义一些变量，qmake将替换为相关值：

| **Placeholder (s)**                                | **效果**                                                                                                                           |
| -------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| ${PRODUCT\_BUNDLE\_IDENTIFIER}, @BUNDLEIDENTIFIER@ | 扩展到目标包的包标识符字符串，例如：com.example.myapp。通过连接QMAKE\_TARGET\_BUNDLE\_PREFIX和QMAKE\_BUNDLE的值来确定，由一个句号（。）分隔.                             |
| ${EXECUTABLE\_NAME}, @EXECUTABLE@, @LIBRARY@       | 相当于QMAKE\_APPLICATION\_BUNDLE\_NAME、QMAKE\_PLUGIN\_BUNDLE\_NAME或QMAKE\_FRAMEWORK\_BUNDLE\_NAME的值（取决于所创建的目标的类型），或者如果没有设置之前的值时的目标。 |
| ${ASSETCATALOG\_COMPILER\_APPICON\_NAME}，@ICON@    | 扩展到ICON的值。                                                                                                                       |
| ${QMAKE\_PKGINFO\_TYPEINFO}, @TYPEINFO@            | 扩展到QMAKE\_PKGINFO\_TYPEINFO的值。                                                                                                   |
| ${QMAKE\_FULL\_VERSION}, @FULL\_VERSION@           | 扩展到用三个版本组件表示的VERSION的值。                                                                                                          |
| ${QMAKE\_SHORT\_VERSION}, @SHORT\_VERSION@         | 扩展到用两个版本组件表示的VERSION的值。                                                                                                          |
| ${MACOSX\_DEPLOYMENT\_TARGET}                      | 扩展到QMAKE\_MACOSX\_DEPLOYMENT\_TARGET的值。                                                                                          |
| ${IPHONEOS\_DEPLOYMENT\_TARGET}                    | 扩展到QMAKE\_IPHONEOS\_DEPLOYMENT\_TARGET的值。                                                                                        |
| ${TVOS\_DEPLOYMENT\_TARGET}                        | 扩展到QMAKE\_TVOS\_DEPLOYMENT\_TARGET的值。                                                                                            |
| ${WATCHOS\_DEPLOYMENT\_TARGET}                     | 扩展到QMAKE\_WATCHOS\_DEPLOYMENT\_TARGET的值。                                                                                         |

注：当使用Xcode生成器时，上述${var}风格的占位符会直接被Xcode构建系统取代，而不是由qmake处理。@var@样式占位符只与qmake的Makefile生成器工作，而不是Xcode生成器。

如果为iOS构建，并且.plist文件包含关键的NSPhotoLibraryUsageDescription，qmake将包含一个额外的插件，以添加照片访问支持（例如，QFile/QFileDialog）。有关此键的更多信息，请参见来自苹果的Info.plist文档。

注：大多数情况下，默认的Info.plist已经足够好了。

QMAKE\_IOS\_DEPLOYMENT\_TARGET

注：此变量仅在iOS平台上使用。

指定应用程序支持的最小iOS硬版本。

有关更多信息，请参见Expressing Supported iOS Versions。

QMAKE\_LFLAGS

指定传递给链接器的一组常规标志。如果需要更改用于特定平台或项目类型的标志，请使用其中一个专门的变量，而不是此变量。

QMAKE\_LFLAGS\_CONSOLE

注：此变量仅在Windows上使用。

指定要构建控制台程序的链接器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LFLAGS\_DEBUG

指定调试构建的链接器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LFLAGS\_PLUGIN

指定要构建插件的链接器标志。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LFLAGS\_RPATH

注：此变量仅在Unix平台上使用。

指定使用QMAKE\_RPATHDIR中的值所需的链接器标志。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LFLAGS\_REL\_RPATH

指定在QMAKE\_RPATHDIR中启用相对路径所需的链接器标志。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_REL\_RPATH\_BASE

指定动态链接器可理解为引用的可执行文件或库的位置的字符串。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LFLAGS\_RPATHLINK

指定使用QMAKE\_RPATHLINKDIR中的值所需的链接器标志。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LFLAGS\_RELEASE

指定发布版本的链接器标志。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LFLAGS\_RELEASE\_WITH\_DEBUGINFO

指定在配置中设置force\_debug\_info的发布构建的链接器标志。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LFLAGS\_APP

指定要构建应用程序的链接器标志。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LFLAGS\_SHLIB

指定用于构建共享库的链接器标志。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LFLAGS\_SONAME

指定用于设置共享对象名称的链接器标志，如.so或.dll。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LFLAGS\_THREAD

指定用于构建多线程项目的链接器标志。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LFLAGS\_WINDOWS

注：此变量仅在Windows上使用。

指定用于构建WindowsGUI项目（即非控制台应用程序）的链接器标志。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LIBDIR

指定所有项目的库搜索路径的列表。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

要在项目文件中指定其他搜索路径，请使用类似的LIBS：

LIBS += -L/path/to/libraries

QMAKE\_LIBDIR\_POST

为所有项目指定系统库搜索路径的列表。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LIBDIR\_FLAGS

注：此变量仅在Unix平台上使用。

指定前缀为-L的所有库目录的位置。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LIBDIR\_EGL

指定EGL与OpenGL/ES或OpenVG一起使用时EGL库目录的位置。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LIBDIR\_OPENGL

指定OpenGL库目录的位置。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

如果OpenGL实现使用EGL(大多数OpenGL/ES系统)，那么可能还需要设置QMAKE\_LIBDIR\_EGL。

QMAKE\_LIBDIR\_OPENVG

指定OpenVG库目录的位置。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

如果OpenVG实现使用EGL，那么可能还需要设置QMAKE\_LIBDIR\_EGL。

QMAKE\_LIBDIR\_X11

注：此变量仅在Unix平台上使用。

指定X11库目录的位置。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LIBS

指定每个项目需要链接的其他库。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

若要在项目文件中指定库，请使用LIBS代替。

QMAKE\_LIBS\_PRIVATE

指定每个项目需要链接的其他专用库。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

若要在库项目文件中指定私有库，请使用LIBS\_PRIVATE代替。

QMAKE\_LIBS\_EGL

指定在使用OpenGL/ES或OpenVG构建Qt时的所有EGL库。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。通常的值是-lEGL。

QMAKE\_LIBS\_OPENGL

指定所有OpenGL库。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

如果OpenGL实现使用EGL(大多数OpenGL/ES系统)，那么可能还需要设置QMAKE\_LIBS\_EGL。

QMAKE\_LIBS\_OPENGL\_ES1, QMAKE\_LIBS\_OPENGL\_ES2

这些变量指定了OpenGLES1和OpenGLES2的所有OpenGL库。

这些变量的值通常由qmake或qmake.conf处理，很少需要修改。

如果OpenGL实现使用EGL(大多数OpenGL/ES系统)，那么可能还需要设置QMAKE\_LIBS\_EGL。

QMAKE\_LIBS\_OPENVG

指定所有的OpenVG库。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。通常的值是-lOpenVG。

一些OpenVG引擎是在OpenGL之上实现的。这将在配置时被检测到，QMAKE\_LIBS\_OPENGL将隐式添加到QMAKE\_LIBS\_OPENVG，无论OpenVG库链接的地方。

如果OpenVG实现使用EGL，那么可能还需要设置QMAKE\_LIBS\_EGL。

QMAKE\_LIBS\_THREAD

注：此变量仅在Unix平台上使用。

指定在构建多线程目标时需要针对其进行链接的所有库。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LIBS\_X11

注：此变量仅在Unix平台上使用。

指定所有X11库。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LIB\_FLAG

如果指定了库模板，则此变量不为空。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LINK

指定在构建基于应用程序的项目时将使用的链接器。只要在处理Makefile时，它是在PATH变量中包含的路径上，就只需要指定链接器可执行文件的文件名。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LINK\_SHLIB\_CMD

指定在创建共享库时要执行的命令。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LN\_SHLIB

指定在创建到共享库的链接时要执行的命令。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_LRELEASE\_FLAGS

通过CONFIG+=lrelease启用时传递给lrelease的其他选项列表。

QMAKE\_OBJECTIVE\_CFLAGS

指定用于构建项目的目标C/C++编译器标志。这些标志除了QMAKE\_CFLAGS和QMAKE\_CXXFLAGS之外使用。

QMAKE\_POST\_LINK

指定在将目标连接在一起后要执行的命令。此变量通常是空的，因此不执行任何内容。

注：此变量对Xcode项目没有影响。

QMAKE\_PRE\_LINK

指定要在将目标连接在一起之前执行的命令。此变量通常是空的，因此不执行任何内容。

注：此变量对Xcode项目没有影响。

QMAKE\_PROJECT\_NAME

注：此变量仅用于VisualStudio项目文件。

在为IDE生成项目文件时，确定项目的名称。默认值为目标的名称。

这个变量的值通常由qmake处理，很少需要修改。

QMAKE\_PROVISIONING\_PROFILE

注：此变量仅用于macOS、iOS、tvOS和watchOS。

有效配置文件配置文件的UUID。与QMAKE\_DEVELOPMENT\_TEAM一起使用以指定配置配置文件。

注：指定预配置的配置文件将禁用自动托管签名。

QMAKE\_MAC\_SDK

此变量在构建通用二进制文件时用于macOS。

QMAKE\_MACOSX\_DEPLOYMENT\_TARGET

注：此变量仅在macOS平台上使用。

指定应用程序所支持的macOS的最低硬版本。

有关更多信息，请参见macOS版本依赖项。

QMAKE\_MAKEFILE

指定要创建的Makefile的名称。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_QMAKE

包含qmake可执行文件的绝对路径。

注：不要试图覆盖此变量的值。

QMAKE\_RESOURCE\_FLAGS

此变量用于自定义在使用它的每个构建规则中传递给资源编译器的选项列表。例如，下面一行确保在每次调用rcc时都使用了-threshold和-compress选项：

QMAKE\_RESOURCE\_FLAGS += -threshold 0 -compress 9

QMAKE\_RPATHDIR

注：此变量仅在Unix平台上使用。

指定在链接时被添加到可执行文件中的库路径的列表，以便在运行时将优先搜索这些路径。

当指定了相对路径时，qmake将把它们映射成动态链接器所理解的相对于引用的可执行文件或库的位置的形式。这只有一些平台(目前是Linux和基于达尔文的平台)支持，并且可以通过检查是否设置了QMAKE\_REL\_RPATH\_BASE来检测。

QMAKE\_RPATHLINKDIR

为静态链接器指定一个库路径列表，以搜索共享库的隐式依赖关系。有关更多信息，请参见ld(1)的手册页。

QMAKE\_RUN\_CC

指定构建对象所需的各个规则。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_RUN\_CC\_IMP

指定构建对象所需的各个规则。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_RUN\_CXX

指定构建对象所需的各个规则。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_RUN\_CXX\_IMP

指定构建对象所需的各个规则。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_SONAME\_PREFIX

如果定义，此变量的值将用作预先构建的共享库的SONAME标识符的路径。SONAME是动态链接器以后将用于引用库的标识符。通常，此引用可以是库名称或完整的库路径。在macOS、iOS、tvOS和watchOS上，可以使用以下占位符相对指定路径：

| 占位符               | 效果                                         |
| ----------------- | ------------------------------------------ |
| @rpath            | 扩展到当前进程可执行文件或引用库中由LC\_RPATH mach-o命令定义的路径。 |
| @executable\_path | 扩展到当前进程的可执行位置。                             |
| @loader\_path     | 扩展到引用的可执行文件或库的位置。                          |

在大多数情况下，使用@rpath就足够了，建议使用：

\# \<project root>/project.pro

QMAKE\_SONAME\_PREFIX = @rpath

但是，也可以使用不同的占位符或绝对路径来指定前缀，例如以下方法之一：

LIBS += -L/path/to/libraries

有关详细信息，请参见关于动态库安装名称的dyld文档。

QMAKE\_TARGET

指定项目目标的名称。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

QMAKE\_TARGET\_COMPANY

仅限Windows。指定项目目标的公司；这可适用于将公司名称放入应用程序的属性中。只有当设置了VERSION或RC\_ICONS变量，而没有设置RC\_FILE和RES\_FILE变量时，才会使用此方法。

QMAKE\_TARGET\_DESCRIPTION

仅限Windows。指定项目目标的描述；这将适用于将描述放到应用程序的属性中。只有当设置了VERSION或RC\_ICONS变量，而没有设置RC\_FILE和RES\_FILE变量时，才会使用此方法。

QMAKE\_TARGET\_COPYRIGHT

仅限Windows。指定项目目标的版权信息；它用于将版权信息输入应用程序的属性。只有当设置了VERSION或RC\_ICONS变量，而没有设置RC\_FILE和RES\_FILE变量时，才会使用此方法。

QMAKE\_TARGET\_PRODUCT

仅限Windows。指定项目目标的产品；它可用于将产品纳入应用程序的属性。只有当设置了VERSION或RC\_ICONS变量，而没有设置RC\_FILE和RES\_FILE变量时，才会使用此方法。

QMAKE\_MANIFEST

仅限Windows。指定项目目标的清单文件。这仅在没有设置RC\_FILE和RES\_FILE变量时被使用。

不要忘记从CONFIG变量中删除embed\_manifest\_exe和embed\_manifest\_dll，否则它将与编译器生成的编译器发生冲突。

QMAKE\_TVOS\_DEPLOYMENT\_TARGET

注：此变量仅在tvOS平台上使用。

指定应用程序支持的tvOS的硬最小版本。

有关更多信息，请参见表达式受支持的iOS版本。

QMAKE\_UIC\_FLAGS

此变量用于自定义在使用它的每个构建规则中传递给用户接口编译器(UIC)的选项列表。

QMAKE\_WATCHOS\_DEPLOYMENT\_TARGET

注：此变量仅在watchOS平台上使用。

指定应用程序所支持的watchOS的最低硬版本。

有关更多信息，请参见表达式受支持的iOS版本。

QML\_IMPORT\_MAJOR\_VERSION

指定要用于自动生成的QML类型注册的主要版本。有关更多信息，请参见从C++中定义QML类型。

QML\_IMPORT\_MINOR\_VERSION

当自动注册C++中定义的QML类型时，使用此次要版本注册模块的附加版本。通常，要注册的次要版本是从元对象中推断出来的。

如果元对象没有更改，并且仍然希望导入具有较新的小版本号的QML模块，则可以使用此变量。例如，MyModule元对象处于1.1级，但您希望将该模块导入为1.3级。

QML\_IMPORT\_VERSION

将QML\_IMPORT\_MAJOR\_VERSION和QML\_IMPORT\_MINOR\_VERSION指定为<主>.<次要>版本字符串。

QML\_IMPORT\_NAME

指定要用于自动生成的QML类型注册的模块名称。有关更多信息，请参见从C++中定义QML类型。

QML\_FOREIGN\_METATYPES

指定在生成JSON类型文件时考虑的元类型的其他JSON文件。当外部库提供公开给QML的类型时，可以直接使用它，或作为基本类型或其他类型的属性。Qt类型将自动被考虑，并且不必在这里添加。

QT

指定您的项目所使用的Qt模块。有关要为每个模块添加的值，请参阅模块文档。

在C++实现级别，使用Qt模块使其头文件可用于包含，并导致其链接到二进制文件。

默认情况下，QT包含core和gui，从而确保可以在没有进一步配置的情况下构建标准的GUI应用程序。

如果您想构建一个没有QtGUI模块的项目，则需要使用“-=”操作符排除GUI值。下面的一行将导致正在构建一个最小的Qt项目：

如果您的项目是一个Qt设计师插件，请使用值uiplugin来指定该项目将作为一个库构建，但有特定的插件支持Qt设计师。有关详细信息，请参见构建和安装插件。

QTPLUGIN

指定要与应用程序链接的静态Qt插件的名称列表，以便它们作为内置资源可用。

qmake自动添加使用的Qt模块通常需要的插件(参见QT)。这些默认值会被调整为一个最佳的开箱即用的体验。参见静态插件的可用插件列表，以及覆盖自动链接的插件。

当链接到Qt的共享/动态构建时，或当链接库时，该变量目前没有影响。它可以用于以后部署动态插件。

QT\_VERSION

包含Qt的当前版本。

QT\_MAJOR\_VERSION

包含Qt的当前主要版本。

QT\_MINOR\_VERSION

包含Qt的当前次要版本。

QT\_PATCH\_VERSION

包含Qt的当前补丁版本。

RC\_FILE

仅限Windows。指定目标对象的Windows资源文件(.rc)的名称。请参见添加Windows资源文件。

RC\_CODEPAGE

仅限Windows。指定应该在生成的.rc文件中指定的代码页。只有当设置了VERSION或RC\_ICONS变量，而没有设置RC\_FILE和RES\_FILE变量时，才会使用此方法。

RC\_DEFINES

仅限Windows。qmake将此变量的值添加为RC预处理器宏(/d选项)。如果未设置此变量，则改为使用DEFINE变量。

RC\_DEFINES += USE\_MY\_STUFF.

RC\_ICONS

仅限Windows。指定应该包含在已生成的.rc文件中的图标。只有在没有设置RC\_FILE和RES\_FILE变量时使用。关于生成.rc文件的更多细节可以在系统平台说明中找到。

RC\_LANG

仅限Windows。指定应该在生成的.rc文件中指定的语言。只有当设置了VERSION或RC\_ICONS变量，而没有设置RC\_FILE和RES\_FILE变量时，才会使用此方法。

RC\_INCLUDEPATH

指定传递给Windows资源编译器的include路径。

RCC\_DIR

指定Qt资源编译器输出文件的目录。

例如：

unix:RCC\_DIR = ../myproject/resources

win32:RCC\_DIR = c:/myproject/resources.

REQUIRES

指定一个将作为条件进行计算的值的列表。如果任何条件是假的，qmake将在构建时跳过这个项目（及其子项）。

注：如果您想在构建时跳过项目或子项目，我们建议使用requires()函数。

RESOURCES

指定目标对象的资源收集文件(qrc)的名称。有关资源收集文件的更多信息，请参见Qt资源系统。

RES\_FILE

仅限Windows。指定此目标的Windows资源编译器的输出文件的名称。请参见RC\_FILE和添加Windows资源文件。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

SOURCES

指定项目中的所有源文件的名称。

例如：

SOURCES = myclass.cpp \\

login.cpp \\

mainwindow.cpp

另请参见HEADERS。

SUBDIRS

当与subdirs的模板(template)一起使用时，此变量将指定包含需要构建的项目部分的所有子目录或项目文件的名称。使用此变量指定的每个子目录都必须包含其自己的项目文件。

建议每个子目录中的项目文件具有与子目录本身相同的基名，因为这样就可以省略该文件名。例如，如果该子目录名为myapp，则该目录中的项目文件应该名为myapp.pro。

或者，您也可以在任何目录中指定一个.pro文件的相对路径。强烈建议您只指定当前项目的父目录或其子目录中的路径。

例如：

SUBDIRS = kernel \\

tools \\

myapp

如果需要确保子目录按特定顺序构建，请对相关的SUBDIRS元素使用.depends修改符。

例如：

SUBDIRS += my\_executable my\_library tests doc

my\_executable.depends = my\_library

tests.depends = my\_executable

上面的配置确保了my\_library在my\_executable之前构建，并且my\_executable是在测试之前构建。但是，doc可以与其他子目录并行构建，从而加快了构建过程。

注：可以列出多个依赖项，并且它们都将在依赖于它们的目标之前构建。

注：不鼓励使用CONFIG+=ordered，因为它可以减缓多核构建。与上面所示的示例不同，所有的构建都将按顺序发生，即使它们没有依赖关系。

除了定义构建顺序之外，还可以通过为子目录元素提供额外的修改符来修改子目录的默认行为。支持的修改器有：

| **修改器**   | **效果**                                    |
| --------- | ----------------------------------------- |
| .subdir   | 使用指定的子目录，而不是SUBDIR的值。                     |
| .file     | 显式地指定子项目pro文件。不能与.subdir修改符一起使用。          |
| .depends  | 此子项目依赖于指定的子项目。                            |
| .makefile | 子项目的makefile。仅在使用makefile的平台上可用。          |
| .target   | 用于创建与此子项目相关的文件目标的基字符串。仅在使用makefile的平台上可用。 |

例如，定义两个子目录，这两个子目录都位于与子目录值不同的目录中，并且其中一个子目录必须大于另一个子目录：

SUBDIRS += my\_executable my\_library

my\_executable.subdir = app

my\_executable.depends = my\_library

my\_library.subdir = lib

TARGET

指定目标文件的名称。默认情况下，包含项目文件的基本名称。

例如：

TEMPLATE = app

TARGET = myapp

SOURCES = main.cpp

上面的项目文件将在unix上生成一个名为myapp的可执行文件，在Windows上生成一个名为myapp.exe的可执行文件。

TARGET\_EXT

指定TARGET的扩展名。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

TARGET\_X

指定具有主要版本号的TARGET的扩展名。

这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

TARGET\_X.Y.Z

指定具有版本号的TARGET的扩展名。这个变量的值通常由qmake或qmake.conf处理，很少需要修改。

TEMPLATE

指定要在生成项目时使用的模板的名称。允许的值为：

| **选项**  | **描述**                                                                                                                           |
| ------- | -------------------------------------------------------------------------------------------------------------------------------- |
| app     | 为构建应用程序创建一个Makefile（默认值）。有关更多信息，请参见构建应用程序。                                                                                       |
| lib     | 创建用于构建库的Makefile。有关更多信息，请参见构建一个库。                                                                                                |
| subdirs | 创建用于在子目录中构建目标的Makefile。子目录是使用SUBDIRS的变量来指定的。                                                                                     |
| aux     | <p>创建一个不构建任何东西的创建文件。如果不需要调用编译器来创建目标，则可以使用此选项；例如，因为您的项目是用解释式语言编写的。</p><p>注：此模板类型仅适用于基于makefile的生成器。特别是，它不能使用vcxproj和Xcode生成器。</p> |
| vcapp   | 仅限Windows。为VisualStudio创建一个应用程序项目。有关更多信息，请参见创建VisualStudio项目文件。                                                                  |
| vclib   | 仅限Windows。为VisualStudio创建一个库项目。                                                                                                  |

例如：

TEMPLATE = lib

SOURCES = main.cpp

TARGET = mylib

可以通过使用-t命令行选项指定一个新的模板类型来覆盖该模板。这将在处理完.pro文件_之后_覆盖模板类型。对于使用模板类型来确定项目是如何构建的的.pro文件，有必要在命令行上声明模板，而不是使用-t选项。

TRANSLATIONS

指定包含将用户界面文本翻译为非本地语言的翻译(.ts)文件列表。

TRANSLATIONS中的翻译文件将由lrelease和使用的lupdate工具进行处理。如果您只想处理一个文件，请使用EXTRA\_TRANSLATIONS。

您可以使用CONFIG+=lrelease在构建过程中自动编译文件，并CONFIG+=lrelease embed\_translations使它们在Qt资源系统中可用。

有关Qt的国际化(i18n)和本地化(l10n)的更多信息，请参阅Qt语言家手册。

UI\_DIR

指定uic中所有中间文件的目录。

例如：

unix:UI\_DIR = ../myproject/ui

win32:UI\_DIR = c:/myproject/ui

VERSION

如果指定了app模板，则指定应用程序的版本号，如果指定了lib模板，则指定库的版本号。

在Windows上，如果未设置RC\_FILE和RES\_FILE变量，则会触发.rc文件的自动生成。生成的.rc文件将有FILEVERSION和PRODUCTVERSION的条目，其中填充了主要、次要、补丁级别和内部版本号。每个数字必须在0到65535的范围内。关于生成.rc文件的更多细节可以在系统平台说明中找到。

例如：

win32:VERSION = 1.2.3.4 # major.minor.patch.build

else:VERSION = 1.2.3 # major.minor.patch

VERSION\_PE\_HEADER

仅限Windows。指定版本号，Windows的链接器通过/versons选项将其放入.exe或.dll文件的头中。只能指定大版本和小版本。如果没有设置VERSION\_PE\_HEADER，它就会从VERSION（如果设置了）回退到主版本和小版本。

VER\_MAJ

如果指定了lib模板，则指定库的主要版本号。

VER\_MIN

如果指定了lib模板，则指定库的次要版本号。

VER\_PAT

如果指定了lib模板，则指定库的补丁版本号。

VPATH

告诉qmake在哪里搜索它无法打开的文件。例如，如果qmake查找源代码并找到一个它无法打开的条目，它会查看整个VPATH列表，看看是否可以自己找到该文件。

另请参见DEPENDPATH。

WINDOWS\_TARGET\_PLATFORM\_VERSION

指定目标Windows版本；这对应于vcxproj文件中的标记Windows目标平台版本。

在桌面Windows上，默认值是环境变量WindowsSDKVersion值。

在通用视窗平台(UWP)上，默认值是环境变量版本的值UCRTVERSION。

WINDOWS\_TARGET\_PLATFORM\_MIN\_VERSION

指定Windows目标平台的最低版本；这对应于vcxproj文件中的标记Windows目标平台最小版本。

默认值为WINDOWS\_TARGET\_PLATFORM\_VERSION。

WINRT\_MANIFEST

指定要传递给UWP上的应用程序清单的参数。允许的值为：

| **成员**               | **描述**                                                            |
| -------------------- | ----------------------------------------------------------------- |
| architecture         | 目标的架构。默认为VCPROJ\_ARCH。                                            |
| background           | 背景颜色。默认为green。                                                    |
| capabilities         | 指定要添加到功能列表中的功能。                                                   |
| capabilities\_device | 指定要添加到功能列表中的设备功能（位置、网络摄像头等）。                                      |
| CONFIG               | 指定用于处理输入清单文件的其他标志。目前，verbatim(逐字处理)是唯一可用的选择。                      |
| default\_language    | 该应用程序的默认语言代码。默认为“en”。                                             |
| dependencies         | 指定程序包所需的依赖项。                                                      |
| description          | 包描述。默认为Default package description。                               |
| foreground           | 前景（文本）颜色。默认为light。                                                |
| iconic\_tile\_icon   | 图标磁贴模板图标的图像文件。由mkspec提供的默认值。                                      |
| iconic\_tile\_small  | 小型图标磁贴模板徽标的图像文件。由mkspec提供的默认值。                                    |
| identity             | 该应用程序的唯一ID。默认重用现有生成的清单的UUID，如果没有，则生成新的UUID。                       |
| logo\_30x30          | logo图像文件的大小为30x30像素。                                              |
| logo\_41x41          | logo图像文件的大小为41x41像素。此参数已过时。                                       |
| logo\_70x70          | logo图像文件的大小为70x70像素。                                              |
| logo\_71x71          | logo图像文件的大小为71x71像素。此参数已过时。                                       |
| logo\_150x150        | logo图像文件的大小为150x150像素。所有WindowsStore应用程序平台都支持此功能。                 |
| logo\_310x150        | logo图像文件的大小为310x150像素。所有WindowsStore应用程序平台都支持此功能。                 |
| logo\_310x310        | logo图像文件的大小为310x310像素。所有WindowsStore应用程序平台都支持此功能。                 |
| logo\_620x300        | 飞屏图像文件的大小为620x300像素。                                              |
| logo\_480x800        | 飞屏图像文件的大小为480x800像素。此参数已过时。                                       |
| logo\_large          | 大型徽标图像文件。这必须是150x150像素。在所有 Windows 应用商店应用平台上均受支持。由 mkspec 提供的默认值。 |
| logo\_medium         | 中标识图像文件。该图像的像素大小必须为70x70。由mkspec提供的默认值。                           |
| logo\_small          | 小标志图像文件。该图像的像素大小必须为30x30。由mkspec提供的默认值。                           |
| logo\_splash         | 飞屏图像文件。该图像的像素大小必须为620x300。由mkspec提供的默认值。                          |
| logo\_store          | 为Windows存储的标志图像文件。由mkspec提供的默认值。                                  |
| logo\_wide           | 宽标志图像文件。这必须是310x150像素。支持所有WindowsStore应用程序平台。由mkspec提供的默认值。       |
| name                 | 显示给用户的程序包的名称。默认为TARGET。                                           |
| phone\_product\_id   | 该产品的GUID。此参数已过时。                                                  |
| phone\_publisher\_id | 发布商的GUID。此参数已过时。                                                  |
| publisher            | 显示发布者的名称。默认为Default publisher display name。                       |
| publisherid          | 发布商的可分辨名称（默认名称：CN=MyCN）                                           |
| target               | 目标对象的名称（.exe）。默认设置为TARGET。                                        |
| version              | 该程序包的版本号。默认为1.0.0.0。                                              |
| minVersion           | 运行该软件包所需的最低Windows版本。默认值为WINDOWS\_TARGET\_PLATFORM\_VERSION。      |
| maxVersionTested     | 该包已测试的最大Windows版本。默认值为WINDOWS\_TARGET\_PLATFORM\_MIN\_VERSION。    |

您可以使用这些值的任何组合。

例如：

WINRT\_MANIFEST.publisher = MyCompany

WINRT\_MANIFEST.logo\_store = someImage.png

WINRT\_MANIFEST.capabilities += internetClient

WINRT\_MANIFEST.capabilities\_device += location

此外，还可以通过使用WINRT\_MANIFEST来指定一个输入清单文件。

例如：

WINRT\_MANIFEST = someManifest.xml.in

如果不应该处理输入的清单文件，而只需要复制到目标目录，则需要设置逐字配置。

WINRT\_MANIFEST = someManifest.xml.in

WINRT\_MANIFEST.CONFIG += verbatim

YACCSOURCES

指定要包含在项目中的Yacc源文件的列表。所有依赖项、头文件和源文件都将自动包含在项目中。

例如：

\_PRO\_FILE\_

正在使用的项目文件的路径。

例如，以下一行导致将项目文件的位置写到控制台：

注：不要试图覆盖此变量的值。

\_PRO\_FILE\_PWD\_

正在使用的项目文件所在的目录的路径。

例如，以下该行会导致将包含项目文件的目录的位置写入控制台：

message(\$$\_PRO\_FILE\_PWD\_)

注：不要试图覆盖此变量的值。
