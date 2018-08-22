title: 逆向学习笔记（1）-为什么代码不停地循环运行
date: 2017-03-09 16:33:07
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


对于下面这段c语言代码会一直不停地循环，为什么呢？

```c
#include<stdio.h>

void HelloWorld()
{
	int i = 0;
	int a[] = {1,2,3,4,5,6,7,8,9,10};
	for(i=0; i<=10; i++)
	{
		a[i] = 0;
		printf("Hello World!\n");
	}
}

int main(int argc, char* argv[])
{
	HelloWorld();
	getchar();
	return 0;
}

```
<!--more-->
# 问题
当你运行上面这串代码的时候，因为c语言并不会对数组越界进行检查，所以是不会报错可以直接运行的，那么是什么原因导致了下面这张图的结果呢？

![GIF.gif](https://ooo.0o0.ooo/2017/03/09/58c11551a36ea.gif)

# 分析
我们可以调试跟进看看，在HelloWorld函数上加一个断点跟进去看看

![snipaste_20170309_165830.png](https://ooo.0o0.ooo/2017/03/09/58c11956dee36.png)

这个函数主要的汇编代码如下
```assembly
8:        int i = 0;
00401038   mov         dword ptr [ebp-4],0
9:        int a[] = {1,2,3,4,5,6,7,8,9,10};
0040103F   mov         dword ptr [ebp-2Ch],1
00401046   mov         dword ptr [ebp-28h],2
0040104D   mov         dword ptr [ebp-24h],3
00401054   mov         dword ptr [ebp-20h],4
0040105B   mov         dword ptr [ebp-1Ch],5
00401062   mov         dword ptr [ebp-18h],6
00401069   mov         dword ptr [ebp-14h],7
00401070   mov         dword ptr [ebp-10h],8
00401077   mov         dword ptr [ebp-0Ch],9
0040107E   mov         dword ptr [ebp-8],0Ah
10:       for(i=0; i<=10; i++)
00401085   mov         dword ptr [ebp-4],0
0040108C   jmp         HelloWorld+77h (00401097)
0040108E   mov         eax,dword ptr [ebp-4]
00401091   add         eax,1
00401094   mov         dword ptr [ebp-4],eax
00401097   cmp         dword ptr [ebp-4],0Ah
0040109B   jg          HelloWorld+97h (004010b7)
11:       {
12:           a[i] = 0;
0040109D   mov         ecx,dword ptr [ebp-4]
004010A0   mov         dword ptr [ebp+ecx*4-2Ch],0
13:           printf("Hello World!\n");
004010A8   push        offset string "Hello World!\n" (0042301c)
004010AD   call        printf (004011a0)
004010B2   add         esp,4
14:       }
004010B5   jmp         HelloWorld+6Eh (0040108e)
15:   }
```

从`int i = 0;`开始看直到`for(i=0; i<=10; i++)`的堆栈图是

![snipaste_20170309_170508.png](https://ooo.0o0.ooo/2017/03/09/58c11ad728904.png)

第一次进入循环开始先把0放到了[ebp-4]，然后跳到了`00401097   cmp dword ptr [ebp-4],0Ah`以及下面的jg，这里的意思是如果ebp-4中存放的值比0A大那么就执行`jg HelloWorld+97h (004010b7)`跳到004010b7函数结束
第一次进入循环时，cmp之后（ebp-4中存放的值比0A小）执行`0040109D`处的语句，此时`ECX`中的值变成了[ebp-4]中的值也就是0，然后`mov dword ptr [ebp+ecx*4-2Ch],0`将0放到`ebp+ecx*4-2Ch`处也就是`EBP-2C`处，下面的两条语句不用管是执行输出的，然后到了`add  esp,4`将栈顶的值加4，这里我们无需关注栈顶，然后`jmp HelloWorld+6Eh (0040108e)`跳回到`0040108e`继续执行

![snipaste_20170309_170508.png](https://ooo.0o0.ooo/2017/03/09/58c11e41e5384.png)

跳到`0040108E   mov  eax,dword ptr [ebp-4]`开始执行，紧接着这三条语句的作用是把`EBP-4`中的值加了1，也就是`EBP-4`中的值现在为1
```assembly
mov eax,dword ptr [ebp-4]
add eax,1
mov dword ptr [ebp-4],eax
```
cmp比较之后再次执行循环体，循环体完成后再次跳到`0040108e`，此时`EBP-28`的值变为了0，栈顶esp再次增加了4（这个例子中栈顶是不用关注的）

![snipaste_20170309_172648.png](https://ooo.0o0.ooo/2017/03/09/58c11fe590ae4.png)

紧接着下次执行后

![snipaste_20170309_172805.png](https://ooo.0o0.ooo/2017/03/09/58c1202d748a0.png)

直到这个数组长度为10的数组执行到第十次

![snipaste_20170309_173122.png](https://ooo.0o0.ooo/2017/03/09/58c120f1a650e.png)

此时再次跳转到`0040108e`，然后`EBP-4`中的值再次增加了1，现在也就是`EBP-4`中的值变为了0A，cmp比较之后`EBP-4`中的值依旧不比0A大，接着执行`mov ecx,dword ptr [ebp-4]`，此时ECX的值变成了0A，接着执行`mov dword ptr [ebp+ecx*4-2Ch],0`也就是`mov dword ptr [ebp-4],0`

然后呢，你发现了什么？？？就是他喵的`EBP-4`中的值变成了0

![snipaste_20170309_173729.png](https://ooo.0o0.ooo/2017/03/09/58c12263d566e.png)

变成0代表着什么？？？`EBP-4`中的值是我们拿来干嘛的？是用来和0A进行cmp然后决定是否结束函数的，可是我们辛辛苦苦循环了10次，第11次全泡汤了，唯一的变化就是数组都成了0，栈顶的值变化了不少，然后再次cmp的时候，0和0A比，决定了你还是要循环，不管多少次，最后都会把你用来计数的地址`EBP-4`中的值清零

这也就是为什么上面这段c语言代码会一直不停地循环的原因

**转载请注明出处**
