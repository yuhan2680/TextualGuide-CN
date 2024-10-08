# Textual库指南1:开发工具(Devtools)

此教程翻译自[Textual官方指南](https://textual.textualize.io/guide/devtools/#console)

Textual的命令行指令和texual名字一样。`textual`指令在你开发工具时非常好用。

麻烦您花一些时间查看textual可用的字命令，它在之后会很有用的。

```
textual --help
```

## 运行

`run`指令可以运行textual应用，提供一个python文件路径它就会执行该应用

```
textual run my_app.py
```

上述命令等同于直接执行my_app.py，不过在用命令运行时可以开启或关闭一些功能来debug你的程序，比如`--dev`可以激活控制台

可以通过`run`指令的`help`来查看具体细节

```
textual run --help
```

你可以通过python import来运行Textual应用，下述命令可以导入`music.play`并用Textual模块运行：

```
textual run music.play
```

假设`music.play`中有一个Textual app实例，如果该app有了不同的名字，您可以将其添加在冒号之后：

```
textual run music.play:MusicPlayerApp
```

## 通过命令运行

如果您是通过命令行脚本安装的app，您可以通过`-c`运行它。例如以下指令将会运行textual colors命令：

```
textual run -c textual colors
```

## 服务端

该工具还予许您在浏览器上服务您的应用。可以很高效地将终端app转换为网页应用！

服务端命令和上诉“运行”中的很类似，以下是如何在端上通过python文件启动一个app：

```
textual serve my_app.py
```

您还可以通过命令直接启动Textual应用，例如：

```
textual serve "textual keys"
```

通过模块启动app的语法则与运行完全不同，你需要明确指定包括python的完整的命令。以下是运行Textual的例子：

```
textual serve "python -m textual"
```

Textual的内置网页服务器是很强大的，您可以同时为app的多个实例提供服务！

```
注意⚠️

在开发app时Textual服务端也很有用，您更改了代码之后只需要刷新浏览器就能更新了。
```

Textual服务端app还有一些额外的开关选项，您可以运行以下指令来查看开关列表：

```
textual serve -h
```

## 实时编辑

如果您用`--dev`执行程序，app将会已开发模式运行

```
textual run --dev my_app.py
```

开发模式到特性是实时编辑CSS文件，对CSS的任何改动都会在几毫秒内返回到终端

在迭代app的外观时该特性相当好用。用编辑器打开CSS并在app上运行一个终端时，对CSS做出的编辑几乎会在保存后立马出现

## 控制台

在构建终端应用时，恁无法通过print来debug（或者登录到控制台），因为您写入输出的任何内容都会覆写应用里的内容。Textual到解决方案是用另一个debug控制台记录了输出和新加入的内容以便debug

您需要打开两个终端模拟器，运在其中一个终端中运行以下内容以使用该控制台：

```
textual console
```

您应该会看到Textual开发工具的欢迎消息：

![Welcome Message](https://github.com/yuhan2680/TextualGuide-CN/blob/main/WelcomeMessage.jpeg)

在另一个控制台，在`textual run`的`--dev`状态下运行你的应用：

```
textual run --dev my_app.py
```

您在应用中print的内容都会显示在控制台窗口上。Textual还会将日志消息显示在该窗口以便您debug您的应用

## 增长冗余

Textual会将特定事件写入日志消息，比如说用户按下按钮或者点击终端。位避免生成过多信息，有些时间会被标记为“冗余的”而不被记入。如果您想看到该部分日志消息，您可以添加`-v`开关

```
textual console -v
```

## 减少冗余

日志信息被分类到了不同组里面，`-x`标记可以排除一个组里的所有信息（即不记录那些信息）。这些组分别为：EVENT, DEBUG, INFO, WARNING, ERROR, PRINT, SYSTEM, LOGGING 及 WORKER。消息组将在时间戳之后打印出来。

您可以排除掉多个组。如果您只想保留warning、errors和print报告的话，可以运行以下指令：

```
textual console -x SYSTEM -x EVENT -x DEBUG -x INFO
```

## 自定义端口

您可以通过`--port`选项来指定控制台的运行端口。在有其他Textual软件在默认端口运行时，自定义端口就能派上用场了：

```
textual console --port 7342
```

之后用命令中的`--port`选项让其运行在同一个端口上：

```
textual run --dev --port 7342 my_app.py
```

## Textual log

用log函数可以美观地打印数据结构及其他[Rich](https://rich.readthedocs.io/en/latest/)能显示的东西。

您可以像这样导入log函数：

```
from textual import log
```

以下是一些通过log向控制台写入内容的示例：

```
def on_mount(self) -> None:

    log("Hello, World")  # simple string

    log(locals())  # Log local variables

    log(children=self.children, pi=3.141592)  # key/values

    log(self.tree)  # Rich renderables
```

## Log 方法

有一个很方便的可以登录app和小部件对象的快捷方法，在事件处理程序时很有用，以下是一个例子：

```
from textual.app import App


class LogApp(App):


    def on_load(self):

        self.log("In the log handler!", pi=3.141529)


    def on_mount(self):

        self.log(self.tree)


if __name__ == "__main__":

    LogApp().run()
```

## 日志处理程序

Textual有一个会经由内置日志库的内容写入devtools（开发工具）的[日志处理程序](https://textual.textualize.io/api/logging/#textual.logging.TextualHandler)

```
提示🔔

日志库仅能处理字符串，因此您不能用日志处理程序记录Rich渲染的东西，比如self.tree
```

以下是一个通过日志处理程序配置日志的例子：

```
import logging

from textual.app import App

from textual.logging import TextualHandler


logging.basicConfig(

    level="NOTSET",

    handlers=[TextualHandler()],

)



class LogApp(App):

    """Using logging with Textual."""


    def on_mount(self) -> None:

        logging.debug("Logged via TextualHandler")



if __name__ == "__main__":

    LogApp().run()
```
