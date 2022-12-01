---
title: "Go语言链接器"
date: 2022-11-19T11:14:12+08:00
draft: true
comments: true
ShowReadingTime: true
tags: ["golang","链接器"]
---

## 链接过程
链接过程就是要把编译器生成的一个个目标文件链接成可执行文件。最终得到的文件是分成各种段的，比如数据段、代码段、BSS段等等，运行时会被装载到内存中。各个段具有不同的读写、执行属性，保护了程序的安全运行。

## 主要工作
- 把所有中间目标文件和库文件捆绑成单一可执行文件
- 统一给每个函数和全局变量分配地址
- 填补中间目标文件和库文件中的残缺信息

## go build 拆解
"go build" 拆解成 "go tool compile" 和 "go tool link" 两个步骤
```bash
go tool compile -N demo.go
# 编译产物：demo.o
go tool link -v -o demo.o demo.out
# demo.out 为可执行文件
```
大致过程：
- 把半成品 demo.o 和 runtime.a 捆绑成单一可执行文件 demo.out
- 给每个函数和全局变量分配地址
- 把指令和数据中所有未知的残缺地址值都填充补齐

## Go链接器工作流程
> 源码:$GOROOT/src/cmd/link

| **Index** | **Name**                  | **Comment**                                                  |
| --------- | ------------------------- | ------------------------------------------------------------ |
| 0         | libinit                   | 创建并初始化输出文件                                         |
| 1         | computeTLSOffset          | 处理输出文件中 TLS 相关的信息（尽管 Go 语言不使用 TLS ）     |
| 2         | Archinit                  | 和具体处理器相关的初始化                                     |
| 3         | loadlib                   | 加载中间目标代码需要调用的库                                 |
| 4         | deadcode                  | 消除代码中定义了但是并未使用的函数和全局变量                 |
| 5         | linksetup                 | 设置平台（linux/darwin/windows）相关的flags                  |
| 6         | dostrdata                 | 处理通过命令行参数 -X 定义的字符串                           |
| 7         | dwarfGenerateDebugInfo    | 生成调试信息（汇编和源码的对应关系等）                       |
| 8         | callgraph                 | 生成调用图                                                   |
| 9         | doStackCheck              | 遍历调用树并检查是否有足够的栈空间                           |
| 10        | mangleTypeSym             | 缩减符号表中符号的长度                                       |
| 11        | doelf/dope/docoff/domacho | 格式相关的处理（ELF/PE/COFF/MachO）                          |
| 12        | textbuildid               |                                                              |
| 13        | addexport                 |                                                              |
| 14        | Gentext                   | 插入一些有用的汇编小片段（trampolines, call stubs, etc.）    |
| 15        | textaddress               | 给代码段中的函数分配地址                                     |
| 16        | typelink                  |                                                              |
| 17        | buildinfo                 | 生成 ”.go.buildinfo” 段                                      |
| 18        | pclntab                   |                                                              |
| 19        | findfunctab               | 生成一个快速索引表（根据地址检索函数）                       |
| 20        | dwarfGenerateDebugSyms    | 生成调试信息（符号相关）                                     |
| 21        | symtab                    | 处理符号表（.symtab段）                                      |
| 22        | dodata                    | 处理数据段                                                   |
| 23        | address                   | 给所有的段分配地址                                           |
| 24        | dwarfcompress             | 压缩调试信息                                                 |
| 25        | layout                    | 输出文件布局（分配各个段的 offset）（注意 offset 和 address 的区别） |
| 26        | Asmb                      | 重定位（补充残缺的地址信息）                                 |
| 27        | GenSymsLate               | 生成一些的附加符号                                           |
| 28        | Asmb2                     | 一些平台相关的特殊处理                                       |
| 29        | ......                    | 若干收尾清理工作                                             |



## 参考

[go夜读](https://talkgo.org/)

史斌