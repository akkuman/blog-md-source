title: 最最简单的c语言函数汇编分析
date: 2017-09-06 14:18:26
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


## 0x01 环境
xp+vc6.0

## 0x02 代码
```c
int plus(int x, int y)
{
	return 0;
}
```
<!--more-->
以下是vc6.0的反汇编窗口
```asm
1:    int plus(int x, int y)
2:    {
00401020   push        ebp
00401021   mov         ebp,esp
00401023   sub         esp,40h
00401026   push        ebx
00401027   push        esi
00401028   push        edi
00401029   lea         edi,[ebp-40h]
0040102C   mov         ecx,10h
00401031   mov         eax,0CCCCCCCCh
00401036   rep stos    dword ptr [edi]
3:        return 0;
00401038   xor         eax,eax
4:    }
0040103A   pop         edi
0040103B   pop         esi
0040103C   pop         ebx
0040103D   mov         esp,ebp
0040103F   pop         ebp
00401040   ret
```

## 0x03 分析
```
push      ebp
mov       ebp,esp
sub       esp,40h
//提升栈，为函数腾出空间，为ebp寻址做准备
push      ebx
push      esi
push      edi
//寄存器压栈，保存现场
lea       edi,[ebp-40h]
//将ebp-40h（esp）的具体内存地址存到edi
mov       ecx,10h
//10（十六进制）存入计数寄存器
mov       eax,0xCCCCCCCC
//初始化eax
rep stos  dword ptr [edi]
//用eax中的值初始化到es:[edi]指向的地址，长度为dword，循环执行次数为eax中的值（恰好ebp->esp全部被初始化）
xor       eax,eax
//eax清零
pop       edi
pop       esi
pop       ebx
mov       esp,ebp
pop       ebp
ret
//寄存器出栈，恢复现场，堆栈平衡并返回
```
