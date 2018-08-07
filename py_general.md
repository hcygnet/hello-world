## 处理多个版本的Python
---
一般来说，安装了多个版本的Python，可以通过命令行cmd/开发环境指定编译器

### cmd

`python`启动默认版本编译器

`py -2`启动默认2.x版编译器

`py -3`启动默认3.x版编译器

`py -2.7`启动指定版本编译器

### pip install

系统默认版本Python的pip install可以通过下面两种方式实现

```cmd
pip install numpy
python -m pip install numpy
```

其他版本可以通过以`-m`选项实现

```cmd
py -2.7 -m pip install numpy
py -2 -m pip install numpy
```
