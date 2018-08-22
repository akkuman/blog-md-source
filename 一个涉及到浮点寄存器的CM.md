title: 一个涉及到浮点寄存器的CM
date: 2017-09-18 20:56:04
update: ""
author: me
tags:
- CrackMe
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



这次找小伙伴要了他的一个CM，怎么说呢，这CM让我学到了不少，其实搞出来后感觉不难，就是有不少FPU浮点相关的指令和FPU寄存器完全没学过，查了不少资料，学到了很多  
<!--more-->
打开是这样  
![](http://ww1.sinaimg.cn/large/c0264382gy1fjo0hr07rpj20bv07qq3v.jpg)

无壳程序，我们直接od查找字符串，爆破我就不说了，直接改跳转  
我第一次是找到这个判断的函数开头，一行行快速单步，确实发现了输入，但是后来很多命令不懂意思我也单步，导致看到后来也不知道怎么判断的  
然后我改了策略，先逆着就近看看，怎么才能使条件成立  
```asm
004010EA  |.  F6C4 41       test ah,0x41                             ;  zf=0 -> ah & 0x41 != 0
004010ED  |.  B8 00000000   mov eax,0x0
004010F2  |.  0f95c0        setne al                                 ;  eax=1 -> al=1 -> zf=0
004010F5  |.  8945 D0       mov [local.12],eax
004010F8  |.  837D D0 01    cmp [local.12],0x1
004010FC  |.  0F85 39000000 jnz 测试.0040113B                          ;  要求zf=1，即eax=0x1
00401102  |.  BB 06000000   mov ebx,0x6
00401107  |.  E8 F8FEFFFF   call 测试.00401004
0040110C  |.  68 01030080   push 0x80000301
00401111  |.  6A 00         push 0x0
00401113  |.  68 00000000   push 0x0
00401118  |.  68 04000080   push 0x80000004
0040111D  |.  6A 00         push 0x0
0040111F  |.  68 6D1B4800   push 测试.00481B6D                         ;  成功
00401124  |.  68 04000000   push 0x4
00401129  |.  BB 90164000   mov ebx,测试.00401690
0040112E  |.  E8 36010000   call 测试.00401269
00401133  |.  83C4 34       add esp,0x34
00401136  |.  E9 34000000   jmp 测试.0040116F
0040113B  |>  BB 06000000   mov ebx,0x6
00401140  |.  E8 BFFEFFFF   call 测试.00401004
00401145  |.  68 01030080   push 0x80000301
0040114A  |.  6A 00         push 0x0
0040114C  |.  68 00000000   push 0x0
00401151  |.  68 04000080   push 0x80000004
00401156  |.  6A 00         push 0x0
00401158  |.  68 721B4800   push 测试.00481B72                         ;  失败
0040115D  |.  68 04000000   push 0x4
```

也就是说需要找ah相关的  
接下来我们整体看看，因为第一次接触FPU浮点相关的指令和FPU寄存器，所以注释写的比较繁琐，望大家见谅  
```asm
0040100C  /.  55            push ebp
0040100D  |.  8BEC          mov ebp,esp
0040100F  |.  81EC 34000000 sub esp,0x34
00401015  |.  6A FF         push -0x1
00401017  |.  6A 08         push 0x8
00401019  |.  68 02000116   push 0x16010002
0040101E  |.  68 01000152   push 0x52010001
00401023  |.  E8 47020000   call 测试.0040126F                         ;  执行后   输入的密码 -> eax(1886b8)
00401028  |.  83C4 10       add esp,0x10
0040102B  |.  8945 FC       mov [local.1],eax                        ;  eax中你的输入 -> ebp-4
0040102E  |.  68 04000080   push 0x80000004
00401033  |.  6A 00         push 0x0
00401035  |.  8B45 FC       mov eax,[local.1]
00401038  |.  85C0          test eax,eax
0040103A  |.  75 05         jnz short 测试.00401041
0040103C  |.  B8 5C1B4800   mov eax,测试.00481B5C
00401041  |>  50            push eax
00401042  |.  68 01000000   push 0x1
00401047  |.  BB A0134000   mov ebx,测试.004013A0
0040104C  |.  E8 18020000   call 测试.00401269                         ;  eax=16进制(你的输入)   ecx=0
00401051  |.  83C4 10       add esp,0x10                             ;  esp = 1000b
00401054  |.  8945 F8       mov [local.2],eax                        ;  16进制(你的输入) -> local.2
00401057  |.  8B5D FC       mov ebx,[local.1]                        ;  你的输入 -> ebx
0040105A  |.  85DB          test ebx,ebx
0040105C  |.  74 09         je short 测试.00401067
0040105E  |.  53            push ebx                                 ;  输入压栈 ebp-38  local.14
0040105F  |.  E8 F9010000   call 测试.0040125D
00401064  |.  83C4 04       add esp,0x4                              ;  [ebp-38] + 4
00401067  |>  DB45 F8       fild [local.2]                           ;  十进制浮点(输入) -> st0
0040106A  |.  DD5D F0       fstp qword ptr ss:[ebp-0x10]             ;  st0 -> ebp-10H
0040106D  |.  DD45 F0       fld qword ptr ss:[ebp-0x10]              ;  ebp-10 -> st0
00401070  |.  DC05 5D1B4800 fadd qword ptr ds:[0x481B5D]             ;  st0 = st0 + 520
00401076  |.  DD5D E8       fstp qword ptr ss:[ebp-0x18]             ;  十进制你的输入+520 -> ebp-18H
00401079  |.  6A FF         push -0x1
0040107B  |.  6A 08         push 0x8
0040107D  |.  68 03000116   push 0x16010003
00401082  |.  68 01000152   push 0x52010001
00401087  |.  E8 E3010000   call 测试.0040126F
0040108C  |.  83C4 10       add esp,0x10
0040108F  |.  8945 E4       mov [local.7],eax
00401092  |.  68 04000080   push 0x80000004
00401097  |.  6A 00         push 0x0
00401099  |.  8B45 E4       mov eax,[local.7]
0040109C  |.  85C0          test eax,eax
0040109E  |.  75 05         jnz short 测试.004010A5
004010A0  |.  B8 5C1B4800   mov eax,测试.00481B5C
004010A5  |>  50            push eax
004010A6  |.  68 01000000   push 0x1
004010AB  |.  BB A0134000   mov ebx,测试.004013A0
004010B0  |.  E8 B4010000   call 测试.00401269
004010B5  |.  83C4 10       add esp,0x10
004010B8  |.  8945 E0       mov [local.8],eax
004010BB  |.  8B5D E4       mov ebx,[local.7]
004010BE  |.  85DB          test ebx,ebx
004010C0  |.  74 09         je short 测试.004010CB
004010C2  |.  53            push ebx
004010C3  |.  E8 95010000   call 测试.0040125D
004010C8  |.  83C4 04       add esp,0x4
004010CB  |>  DB45 E0       fild [local.8]                           ;  (641)10 -> st0
004010CE  |.  DD5D D8       fstp qword ptr ss:[ebp-0x28]             ;  st0 -> ebp-28H
004010D1  |.  DD45 E8       fld qword ptr ss:[ebp-0x18]              ;  [ebp-18H](十进制你的输入+520) -> st0
004010D4  |.  DC65 D8       fsub qword ptr ss:[ebp-0x28]             ;  st0 = st0 - [ebp-28H]  (641)10
004010D7  |.  D9E4          ftst                                     ;  st0和0.0比较，据此设置FPU状态字C0,C2,C3位
004010D9  |.  DFE0          fstsw ax
004010DB  |.  F6C4 01       test ah,0x1
004010DE  |.  74 02         je short 测试.004010E2
004010E0  |.  D9E0          fchs                                     ;  st0改变符号位
004010E2  |>  DC1D 651B4800 fcomp qword ptr ds:[0x481B65]            ;  st0和[481B65](无限接近0的一个正浮点数)比较，据此设置FPU状态字C0,C2,C3位，并把st0弹到[481B65]
004010E8  |.  DFE0          fstsw ax                                 ;  FPU状态字 -> eax，根据下面可知，FPU状态字C0或C3为1均可
004010EA  |.  F6C4 41       test ah,0x41                             ;  zf=0 -> ah & 0x41 != 0
004010ED  |.  B8 00000000   mov eax,0x0
004010F2  |.  0f95c0        setne al                                 ;  eax=1 -> al=1 -> zf=0
004010F5  |.  8945 D0       mov [local.12],eax
004010F8  |.  837D D0 01    cmp [local.12],0x1
004010FC  |.  0F85 39000000 jnz 测试.0040113B                          ;  要求zf=1，即eax=0x1
00401102  |.  BB 06000000   mov ebx,0x6
00401107  |.  E8 F8FEFFFF   call 测试.00401004
0040110C  |.  68 01030080   push 0x80000301
00401111  |.  6A 00         push 0x0
00401113  |.  68 00000000   push 0x0
00401118  |.  68 04000080   push 0x80000004
0040111D  |.  6A 00         push 0x0
0040111F  |.  68 6D1B4800   push 测试.00481B6D                         ;  成功
00401124  |.  68 04000000   push 0x4
00401129  |.  BB 90164000   mov ebx,测试.00401690
0040112E  |.  E8 36010000   call 测试.00401269
00401133  |.  83C4 34       add esp,0x34
00401136  |.  E9 34000000   jmp 测试.0040116F
0040113B  |>  BB 06000000   mov ebx,0x6
00401140  |.  E8 BFFEFFFF   call 测试.00401004
00401145  |.  68 01030080   push 0x80000301
0040114A  |.  6A 00         push 0x0
0040114C  |.  68 00000000   push 0x0
00401151  |.  68 04000080   push 0x80000004
00401156  |.  6A 00         push 0x0
00401158  |.  68 721B4800   push 测试.00481B72                         ;  失败
0040115D  |.  68 04000000   push 0x4
```

那么回到上面的问题，ah的值是从哪来的，我们在`004010E8`处可以看到，FPU状态码进了eax  
那么根据我们的判断`ah & 0x41 != 0`，能得出对FPU状态字有什么要求呢？  
我们看这张图  
![](http://ww1.sinaimg.cn/large/c0264382gy1fjo0im7jh7j20g70bqt93.jpg)

也就是说  
```
    0100 0001
&   -x-- -yzt
----------------
    真
其中x代表C3，y代表C2，z代表C1,t代表C0
```

根据上面的判断，也就是说**只能C3或C0为1**  
- C3为1的话  
```asm
004010E2  |>  DC1D 651B4800 fcomp qword ptr ds:[0x481B65]            ;  st0和[481B65](无限接近0的一个正浮点数)比较，据此设置FPU状态字C0,C2,C3位，并把st0弹到[481B65]
```
这段就是st0等于一个无限接近0的浮点数才能使C3为1，但是根据我们之前的`st0 = st0 = 你的输入+520-641`，st0不可能是等于一个无限接近0的浮点数，所以**C3为1排除**  [fcomp命令参考处](http://x86.renejeschke.de/html/file_module_x86_id_87.html)
- C0为1的话  
C0为1是怎么来的呢？只有两个地方涉及到了FPU状态字的改变，分别是`4010D7`和`4010E2`  
`4010E2`处要使C0为1，必须st0小于那个无限接近0的浮点数，这个条件不足以我们判断，接着往上看  
`4010D7`处要使C0为1，必须st0等于0.0，也就是`你的输入+520-641=0`  [ftst命令参考处](http://x86.renejeschke.de/html/file_module_x86_id_123.html)

所以至此我们就得到了密码  
密码+520-641=0      ==>     密码=121  
![](http://ww1.sinaimg.cn/large/c0264382gy1fjo0ir8du4j20am070a9y.jpg)

这个CM怎么说呢，我刚开始是没想到会涉及到浮点寄存器的，因为我还没学这个，不过后来追到快判断的地方时，发现了FPU状态码进入eax参与过程了，然后查了关于FPU 状态寄存器的资料，就可以搞出来了
