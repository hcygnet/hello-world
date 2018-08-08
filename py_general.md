## 正确使用 Pycharm 编译器

### packages

建议使用virtualenv安装各种拓展包，
[一个stackoverflow的回答](https://stackoverflow.com/questions/19885821/how-do-i-import-modules-in-pycharm)

> You should never need to modify the path directly,
> either through environment variables or `sys.path`.
> Whether you use the os (ex. `apt-get`), or `pip` in a virtualenv,
> packages will be installed to a location already on the path.
> 
> [The correct way to develop a Python application is with a virtualenv.](https://packaging.python.org/tutorials/installing-packages/#creating-virtual-environments)
> Packages and version are installed without effecting the system or other projects.
> [PyCharm has a built-in interface to create a virtualenv and install packages.](https://www.jetbrains.com/help/pycharm/creating-virtual-environment.html)
> Or you can create it from the command line and then point PyCharm at it.

我的理解：venv相当于提供了一个隔离的编译环境；
在这个环境下操作不会污染系统的py编译器。
所以应当尽量把各种拓展包都装在项目中。

新建项目时，可以选择新建virtualenv环境。

在新建项目后，使用底部工具栏的terminal，
可以发现(venv)。
在这里使用`pip install`等各种操作，都不会影响系统编译器。

```
Microsoft Windows [版本 6.1.7601]
版权所有 (c) 2009 Microsoft Corporation。保留所有权利。

(venv) D:\dataworks\PythonProjects\Python27>
```

所以，安装python编译器后，并不需要急着安装各种包。
根据项目需要在venv下安装才是正确使用方法。

## 处理多个版本的Python

一般来说，安装了多个版本的Python，可以通过命令行cmd/开发环境指定编译器

### cmd

`python`启动默认版本编译器

`py -2`启动默认2.x版编译器

`py -3`启动默认3.x版编译器

`py -2.7`启动指定版本编译器

### pip install

系统默认版本Python的pip install可以通过下面两种方式实现

```
pip install numpy
python -m pip install numpy
```

其他版本可以通过以`-m`选项实现

```
py -2.7 -m pip install numpy
py -2 -m pip install numpy
```
