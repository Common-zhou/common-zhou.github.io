---
layout: post
title:  "异常学习"
categories: 异常
tags: exception
excerpt: 学习异常类型，以及处理方式
auth: zhou
---

* content
{:toc}
# 异常类型

JAVA中有三种一般类型的可抛类: **检查性异常**(checked exceptions)、**非检查性异常**(unchecked Exceptions) 和 **错误**(errors)。



![图片描述](/assets\excepion\exception-structure.jpg)

**检查性异常(checked exceptions)** 是必须在在方法的`throws`子句中声明的异常。它们扩展了异常，旨在成为一种“在你面前”的异常类型。JAVA希望你能够处理它们，因为它们以某种方式依赖于程序之外的外部因素。检查的异常表示在正常系统操作期间可能发生的预期问题。 当你尝试通过网络或文件系统使用外部系统时，通常会发生这些异常。 大多数情况下，对检查性异常的正确响应应该是稍后重试，或者提示用户修改其输入。
**非检查性异常(unchecked Exceptions)** 是不需要在throws子句中声明的异常。 由于程序错误，JVM并不会强制你处理它们，因为它们大多数是在运行时生成的。 它们扩展了`RuntimeException`。 最常见的例子是`NullPointerException` 。 未经检查的异常可能不应该重试，正确的操作通常应该是什么都不做，并让它从你的方法和执行堆栈中出来。 在高层次的执行中，应该记录这种类型的异常。
**错误(errors)** 是严重的运行时环境问题，几乎肯定无法恢复。 例如`OutOfMemoryError`，`LinkageError`和`StackOverflowError`, 它们通常会让程序崩溃或程序的一部分。 只有良好的日志练习才能帮助你确定错误的确切原因。



