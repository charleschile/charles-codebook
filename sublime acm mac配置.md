# sublime使用方法
使用control  b来运行测试
使用control k p 来关闭侧边拦

使用command + 或者command -来调整字体大小

command shif p来打开sublime的命令行

想要更改fast olympic设置使用browse package


### mac上下载gcc
mac自带的gcc使用的是clang，不能支持`#include <bits/stdc++.h>`这个万能头文件

使用homebrew下载gcc
```bash
brew install gcc
```

使用

```bash
brew list gcc
```
来查看gcc版本

我的gcc版本是14

使用

```bash
gcc-14 -v
```

确认gcc-14命令能够被使用

使用
```shell
which gcc-14
```
来查看gcc-14命令的地址

```shell
alias gcc='gcc-14'
alias g++='g++-14'
```