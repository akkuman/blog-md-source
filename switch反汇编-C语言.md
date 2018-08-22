title: switch反汇编(C语言)
date: 2017-09-07 00:25:09
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


在分支较多的时候，switch的效率比if高，在反汇编中我们即可看到效率高的原因
<!--more-->

## 0x01分支结构不超过3个
```c
#include <stdio.h>

void main()
{
	int x = 5;
	switch(x)
	{
	case 5:
		printf("%d\n",x);
		break;
	case 6:
		printf("%d\n",x);
		break;
	case 7:
		printf("%d\n",x);
		break;
	default:
		break;
	}

	return;
}
```
```asm
5:        int x = 5;
00401028   mov         dword ptr [ebp-4],5
6:        switch(x)
7:        {
0040102F   mov         eax,dword ptr [ebp-4]
00401032   mov         dword ptr [ebp-8],eax         //x的值放入[ebp-8]
00401035   cmp         dword ptr [ebp-8],5
00401039   je          main+39h (00401049)           //x与5相等就跳转，下面6和7相同
0040103B   cmp         dword ptr [ebp-8],6
0040103F   je          main+4Ch (0040105c)
00401041   cmp         dword ptr [ebp-8],7
00401045   je          main+5Fh (0040106f)
00401047   jmp         main+72h (00401082)
8:        case 5:
9:            printf("%d\n",x);
00401049   mov         ecx,dword ptr [ebp-4]
0040104C   push        ecx
0040104D   push        offset string "%d\n" (0042201c)
00401052   call        printf (004010d0)
00401057   add         esp,8
10:           break;
0040105A   jmp         main+83h (00401093)
11:       case 6:
12:           printf("%d\n",x);
0040105C   mov         edx,dword ptr [ebp-4]
0040105F   push        edx
00401060   push        offset string "%d\n" (0042201c)
00401065   call        printf (004010d0)
0040106A   add         esp,8
13:           break;
0040106D   jmp         main+83h (00401093)
14:       case 7:
15:           printf("%d\n",x);
0040106F   mov         eax,dword ptr [ebp-4]
00401072   push        eax
00401073   push        offset string "%d\n" (0042201c)
00401078   call        printf (004010d0)
0040107D   add         esp,8
16:           break;
00401080   jmp         main+83h (00401093)
17:       default:
18:           printf("%d\n",x);
00401082   mov         ecx,dword ptr [ebp-4]
00401085   push        ecx
00401086   push        offset string "%d\n" (0042201c)
0040108B   call        printf (004010d0)
00401090   add         esp,8
19:           break;
20:       }
```

## 0x02分支数超过3且分支存在线性关系
```c
#include <stdio.h>

void main()
{
	int x = 5;
	switch(x)
	{
	case 5:
		printf("%d\n",x);
		break;
	case 6:
		printf("%d\n",x);
		break;
	case 7:
		printf("%d\n",x);
		break;
	case 8:
		printf("%d\n",x);
		break;
	case 9:
		printf("%d\n",x);
		break;
	default:
		printf("%d\n",x);
		break;
	}

	return;
}
```
```asm
5:        int x = 5;
0040D778   mov         dword ptr [ebp-4],5
6:        switch(x)
7:        {
0040D77F   mov         eax,dword ptr [ebp-4]
0040D782   mov         dword ptr [ebp-8],eax
0040D785   mov         ecx,dword ptr [ebp-8]
0040D788   sub         ecx,5                              //x减去分支中的最小值5，方便构建跳转表
0040D78B   mov         dword ptr [ebp-8],ecx
0040D78E   cmp         dword ptr [ebp-8],4                
0040D792   ja          $L537+13h (0040d7fd)               //x-5>4跳转到default，即x>9跳转
0040D794   mov         edx,dword ptr [ebp-8]              //edx=x-5
0040D797   jmp         dword ptr [edx*4+40D81Fh]          //构建跳转表，根据edx的值从对应地址取出值(各个分支的地址)，40D81F为跳转表起始地址
8:        case 5:
9:            printf("%d\n",x);
0040D79E   mov         eax,dword ptr [ebp-4]
0040D7A1   push        eax
0040D7A2   push        offset string "%d\n" (0042201c)
0040D7A7   call        printf (004010d0)
0040D7AC   add         esp,8
10:           break;
0040D7AF   jmp         $L537+24h (0040d80e)
11:       case 6:
12:           printf("%d\n",x);
0040D7B1   mov         ecx,dword ptr [ebp-4]
0040D7B4   push        ecx
0040D7B5   push        offset string "%d\n" (0042201c)
0040D7BA   call        printf (004010d0)
0040D7BF   add         esp,8
13:           break;
0040D7C2   jmp         $L537+24h (0040d80e)
14:       case 7:
15:           printf("%d\n",x);
0040D7C4   mov         edx,dword ptr [ebp-4]
0040D7C7   push        edx
0040D7C8   push        offset string "%d\n" (0042201c)
0040D7CD   call        printf (004010d0)
0040D7D2   add         esp,8
16:           break;
0040D7D5   jmp         $L537+24h (0040d80e)
17:       case 8:
18:           printf("%d\n",x);
0040D7D7   mov         eax,dword ptr [ebp-4]
0040D7DA   push        eax
0040D7DB   push        offset string "%d\n" (0042201c)
0040D7E0   call        printf (004010d0)
0040D7E5   add         esp,8
19:           break;
0040D7E8   jmp         $L537+24h (0040d80e)
20:       case 9:
21:           printf("%d\n",x);
0040D7EA   mov         ecx,dword ptr [ebp-4]
0040D7ED   push        ecx
0040D7EE   push        offset string "%d\n" (0042201c)
0040D7F3   call        printf (004010d0)
0040D7F8   add         esp,8
22:           break;
0040D7FB   jmp         $L537+24h (0040d80e)
23:       default:
24:           printf("%d\n",x);
0040D7FD   mov         edx,dword ptr [ebp-4]
0040D800   push        edx
0040D801   push        offset string "%d\n" (0042201c)
0040D806   call        printf (004010d0)
0040D80B   add         esp,8
25:           break;
26:       }

```
跳转表从[edx*4+40D81Fh]取出分支的地址值然后进行jmp，下表是跳转表部分
```
0040D81F  9E D7 40 00  ..@.
0040D823  B1 D7 40 00  ..@.
0040D827  C4 D7 40 00  ..@.
0040D82B  D7 D7 40 00  ..@.
0040D82F  EA D7 40 00  ..@.
```

## 0x03分支跃度大难以构成跳转表的分支结构
```c
#include <stdio.h>

void main()
{
	int x = 5;
	switch(x)
	{
	case 5:
		printf("%d\n",x);
		break;
	case 6:
		printf("%d\n",x);
		break;
	case 7:
		printf("%d\n",x);
		break;
	case 8:
		printf("%d\n",x);
		break;
	case 100:
		printf("%d\n",x);
		break;
	default:
		printf("%d\n",x);
		break;
	}

	return;
}
```
```asm
5:        int x = 5;
00401028   mov         dword ptr [ebp-4],5
6:        switch(x)
7:        {
0040102F   mov         eax,dword ptr [ebp-4]
00401032   mov         dword ptr [ebp-8],eax
00401035   mov         ecx,dword ptr [ebp-8]
00401038   sub         ecx,5
0040103B   mov         dword ptr [ebp-8],ecx
0040103E   cmp         dword ptr [ebp-8],5Fh
00401042   ja          $L536+13h (004010b5)               //具体参见上一种,x大于100跳转到default
00401044   mov         eax,dword ptr [ebp-8]              //edx=x-5
00401047   xor         edx,edx                            //edx置零
00401049   mov         dl,byte ptr  (004010ef)[eax]       //查询索引表并将取出来的值放入DL(在od里面的这条反汇编更清楚)
0040104F   jmp         dword ptr [edx*4+4010D7h]          //根据DL(EDX)的值查跳转表
8:        case 5:
9:            printf("%d\n",x);
00401056   mov         ecx,dword ptr [ebp-4]
00401059   push        ecx
0040105A   push        offset string "%d\n" (0042201c)
0040105F   call        printf (004011a0)
00401064   add         esp,8
10:           break;
00401067   jmp         $L536+24h (004010c6)
11:       case 6:
12:           printf("%d\n",x);
00401069   mov         edx,dword ptr [ebp-4]
0040106C   push        edx
0040106D   push        offset string "%d\n" (0042201c)
00401072   call        printf (004011a0)
00401077   add         esp,8
13:           break;
0040107A   jmp         $L536+24h (004010c6)
14:       case 7:
15:           printf("%d\n",x);
0040107C   mov         eax,dword ptr [ebp-4]
0040107F   push        eax
00401080   push        offset string "%d\n" (0042201c)
00401085   call        printf (004011a0)
0040108A   add         esp,8
16:           break;
0040108D   jmp         $L536+24h (004010c6)
17:       case 8:
18:           printf("%d\n",x);
0040108F   mov         ecx,dword ptr [ebp-4]
00401092   push        ecx
00401093   push        offset string "%d\n" (0042201c)
00401098   call        printf (004011a0)
0040109D   add         esp,8
19:           break;
004010A0   jmp         $L536+24h (004010c6)
20:       case 100:
21:           printf("%d\n",x);
004010A2   mov         edx,dword ptr [ebp-4]
004010A5   push        edx
004010A6   push        offset string "%d\n" (0042201c)
004010AB   call        printf (004011a0)
004010B0   add         esp,8
22:           break;
004010B3   jmp         $L536+24h (004010c6)
23:       default:
24:           printf("%d\n",x);
004010B5   mov         eax,dword ptr [ebp-4]
004010B8   push        eax
004010B9   push        offset string "%d\n" (0042201c)
004010BE   call        printf (004011a0)
004010C3   add         esp,8
25:           break;
26:       }
```
索引表
```
004010F1  00 01 02 03 05 05 05 05  ........
004010F9  05 05 05 05 05 05 05 05  ........
00401101  05 05 05 05 05 05 05 05  ........
00401109  05 05 05 05 05 05 05 05  ........
00401111  05 05 05 05 05 05 05 05  ........
00401119  05 05 05 05 05 05 05 05  ........
00401121  05 05 05 05 05 05 05 05  ........
00401129  05 05 05 05 05 05 05 05  ........
00401131  05 05 05 05 05 05 05 05  ........
00401139  05 05 05 05 05 05 05 05  ........
00401141  05 05 05 05 05 05 05 05  ........
00401149  05 05 05 05 05 05 05 04  ........
```
跳转表
```
004010D9  58 10 40 00  X.@.
004010DD  6B 10 40 00  k.@.
004010E1  7E 10 40 00  ~.@.
004010E5  91 10 40 00  ..@.
004010E9  A4 10 40 00  ..@.
004010ED  B7 10 40 00  ..@.
```
