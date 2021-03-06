+++
categories = ["y"]
date = "2017-08-05T13:00:17+08:00"
description = "收集日常使用的linux命令及脚本"
tags = ["linux", "bash"]
title = "Linux 小技巧"

+++

1. 有道词典(Python3)

```
# 使用命令别名
alias youdao='python3 /opt/skill/youdao.py'
```

2. 文件内部搜索(自动递归子目录, 忽略二进制文件)

```
# 命令别名 findstr for string search within file
alias findstr='find `pwd` -type f |xargs grep -s -i -n --binary-files=without-match'
```

3. Shell 重定向

>
0表示标准输入  
1表示标准输出  
2表示标准错误输出  
> 默认为标准输出重定向，与 1> 相同  
2>&1 意思是把 标准错误输出 重定向到 标准输出.  
&>file 意思是把 标准输出 和 标准错误输出 都重定向到文件file中  


4. Docker 镜像加速

修改(不存在则创建) /etc/docker/daemon.json 文件并添加上 registry-mirrors 键值。

```
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

重启 Docker

```
systemctl daemon-reload
systemctl restart docker
```

5. npm 镜像加速

安装 cnpm 

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

或者使用别名

```
alias cnpm="npm --registry=https://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=https://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"

# Or alias it in .bashrc or .zshrc
$ echo '\n#alias for cnpm\nalias cnpm="npm --registry=https://registry.npm.taobao.org \
  --cache=$HOME/.npm/.cache/cnpm \
  --disturl=https://npm.taobao.org/dist \
  --userconfig=$HOME/.cnpmrc"' >> ~/.zshrc && source ~/.zshrc
```

使用 cnpm 替代 npm

6. clang-formt 配置

```
#基于那个配置文件
BasedOnStyle: LLVM
#指针的*的挨着哪边
PointerAlignment: Right
#缩进宽度
IndentWidth: 4
# 连续的空行保留几行
MaxEmptyLinesToKeep: 1
# 在 @property 后面添加空格, \@property (readonly) 而不是 \@property(readonly).
ObjCSpaceAfterProperty: true
# OC block后面的缩进
ObjCBlockIndentWidth: 4
# 是否允许短方法单行
AllowShortFunctionsOnASingleLine: true
# 是否允许短if单行 If true, if (a) return; 可以放到同一行
AllowShortIfStatementsOnASingleLine: true
#注释对齐
AlignTrailingComments: true
# 换行的时候对齐操作符
#AlignOperands: true
# 中括号两边空格 [] 
SpacesInSquareBrackets: true
# 小括号两边添加空格
SpacesInParentheses : true
#多行声明语句按照=对齐
AlignConsecutiveDeclarations: true
#连续的赋值语句以 = 为中心对齐
AlignConsecutiveAssignments: true
#等号两边的空格
SpaceBeforeAssignmentOperators: true
# 容器类的空格 例如 OC的字典
SpacesInContainerLiterals: true
#缩进
IndentWrappedFunctionNames: true
#在block从空行开始
KeepEmptyLinesAtTheStartOfBlocks: true
#在构造函数初始化时按逗号断行，并以冒号对齐
BreakConstructorInitializersBeforeComma: true
#函数参数换行
AllowAllParametersOfDeclarationOnNextLine: true
#括号后添加空格
SpaceAfterCStyleCast: true
#tab键盘的宽度
TabWidth: 4
UseTab: Never

```

7. Linux 动态库符号导出
	
-fvisibility=hidden
	
>	gcc 在链接时设置 -fvisibility=hidden ，则不加 visibility 声明的都默认为 hidden ; 
>	gcc 默认设置 -fvisibility=default，即全部可见；
>	在 gcc 中加了这个设置之后表示所有的函数都是对外不可见了，
>   然后在代码里面对于想公开的函数加上 __attribute__((visibility("default")))

```
#if defined _WIN32 || defined __CYGWIN__
  #ifdef BUILDING_DLL
    #ifdef __GNUC__
      #define DLL_PUBLIC __attribute__((dllexport))
    #else
      #define DLL_PUBLIC __declspec(dllexport) // Note: actually gcc seems to also supports this syntax.
    #endif
  #else
    #ifdef __GNUC__
      #define DLL_PUBLIC __attribute__((dllimport))
    #else
      #define DLL_PUBLIC __declspec(dllimport) // Note: actually gcc seems to also supports this syntax.
  #endif
  #define DLL_LOCAL
#else
  #if __GNUC__ >= 4
    #define DLL_PUBLIC __attribute__ ((visibility("default")))
    #define DLL_LOCAL  __attribute__ ((visibility("hidden")))
  #else
    #define DLL_PUBLIC
    #define DLL_LOCAL
  #endif
#endif
```

8. Linux 动态库路径

编译时添加RPATH

> -Wl,-rpath=./  

将链接库的目录添加到/etc/ld.so.conf文件中

设置运行是路径

> export LD_LIBRARY_PATH=/opt/gtk/lib:$LD_LIBRARY_PATH

从当前动态库找符号调用,避免调用到其他库的同名符号
 
> -Wl,-Bsymbolic
