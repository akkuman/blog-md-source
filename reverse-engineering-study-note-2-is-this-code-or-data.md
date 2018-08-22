title: 逆向学习笔记（2）-这是代码还是数据
date: 2017-03-10 23:38:22
update: ""
author: me
tags:
- 逆向
categories:
- 逆向
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---


**以下的ide为CodeBlocks，编译器采用的GCC，系统为win10 64bit,在不同编译器和环境下汇编代码可能不同**
<!--more-->
# 现象
```c
#include <stdio.h>
#include <stdlib.h>

int getmin(int a, int b)
{
	if(a>b)
		return b;
	else
		return a;
}

typedef int (*pfunction)(int, int);

int main()
{
    int a=456789,b=123789,c=0;
    pfunction pGetmin = (pfunction)getmin;
	
    c = pGetmin(a, b);
    printf("%d",c);
    return 0;
}
```

上面这段代码是比大小输出小的，`typedef int (*pfunction)(int, int);`定义了一个函数指针，但是下面这段代码和上面的功能是完全一样的

```c
#include <stdio.h>
#include <stdlib.h>

typedef int (*pfunction)(int, int);

int main()
{
    int a=456789,b=123789,c=0;
    unsigned char loc[] =
    {
        0x55, 0x89, 0xE5, 0x8B, 0x45, 0x08, 0x3B, 0x45, 0x0C, 0x7E, 0x05, 0x8B, 0x45, 0x0C, 0xEB, 0x03, 0x8B, 0x45, 0x08, 0x5D, 0xC3
    };

    pfunction getmin = (pfunction)&loc;
    c = getmin(a, b);
    printf("%d",c);
    return 0;
}
```
# 原因分析
当`c = pGetmin(a, b);`调用pGetmin的时候，在汇编中是先call跳到一个地址然后从那个地址再jmp到函数入口地址然后开始执行函数
getmin函数整体汇编为
```assembly
push   ebp
mov    ebp,esp
mov    eax,DWORD PTR [ebp+0x8]
cmp    eax,DWORD PTR [ebp+0xc]
jle    <getmin+16>
mov    eax,DWORD PTR [ebp+0xc]
jmp    <getmin+19>
mov    eax,DWORD PTR [ebp+0x8]
pop    ebp
ret
```
通过一些调试程序（发现CodeBlocks带的汇编调试没有vc6好用，看不到硬编码）可以得出这段汇编代码在硬编码中的值为
```
0x55, 0x89, 0xE5, 0x8B, 0x45, 0x08, 0x3B, 0x45, 0x0C, 0x7E, 0x05, 0x8B, 0x45, 0x0C, 0xEB, 0x03, 0x8B, 0x45, 0x08, 0x5D, 0xC3
```
这段数据我们在第二个代码中把它存入了一个char类型的数组，它虽然在数据区，但是它还是可以看作可运行的一段函数代码，我们依旧定义一个函数指针指向这个char类型数组的入口地址，达到了和第一种相同的效果
在编程中，我们是把代码和数据分得很开的，但是在逆向和汇编中，这个区别就不明显了，在计算机中都是以数据形式存在的，你可以说它是一串数据，也可以说它是代码

***转载请注明出处***
