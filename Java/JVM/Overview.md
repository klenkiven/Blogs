# JVM笔记概述

> JVM部分的笔记是看《深入理解Java虚拟机》所做的，因此JVM笔记这个系列，顺序也会按照这本书的章节安排做相关的笔记。

笔记会分成**三个部分**：

+ 自动内存管理机制（Automatic memory management mechanism, AMMM）

+ 虚拟机执行子系统（Virtual machine execution subsystem, VMES）

+ 程序编译与代码优化（Program compilation and code optimization, PCACP）

+ 高效并发（Efficient concurrency, EC）

  > 这里的并发问题在虚拟机层面分析

## 自动内存管理机制

+ Java内存区域和内存溢出异常（**OOM**）
+ 