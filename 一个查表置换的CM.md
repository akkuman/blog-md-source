title: 一个查表置换的CM
date: 2017-09-20 01:01:15
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



说实话，今天被自己蠢哭了  

因为看多了一个字符，以为是输入字符变形后的base64编码，也怪自己没大致看过base64汇编形式，把base64跟完了用py实现完算法才意思到是base64，这是题外话  
<!--more-->
本人初学者，两天或一天一个cm练练，大家可以与我交流akkumans@qq.com，[我博客](http://hacktech.cn)  

上面的题外话就是今天搞的一个cm，被自己蠢哭了，不过也算是base64编码流程无比清晰了，不算是无用功  

这个cm是一个控制台的，丢到xp无法运行，本机只装了x64dbg(x32dbg)，用这个调试软件来试试吧  
```
C:\Users\Administrator\Desktop>reverse3.exe
Please enter the flag:97103012
wrong input
```

字符串搜索，找到判断的地方
```asm
01241269 | 85 C0                    | test eax,eax                             | zf=1 => eax=0
0124126B | 75 07                    | jne reverse3.1241274                     |
0124126D | 68 78 21 24 01           | push reverse3.1242178                    | 1242178:"this is the right flag"
01241272 | EB 05                    | jmp reverse3.1241279                     |
01241274 | 68 90 21 24 01           | push reverse3.1242190                    | 1242190:"wrong input"
01241279 | FF 15 B0 20 24 01        | call dword ptr ds:[<&puts>]              |
0124127F | 8B 4D FC                 | mov ecx,dword ptr ss:[ebp-4]             |
01241282 | 83 C4 04                 | add esp,4                                |
01241285 | 33 CD                    | xor ecx,ebp                              |
01241287 | 33 C0                    | xor eax,eax                              | eax:"OTacMDMzMTI="
01241289 | E8 92 00 00 00           | call reverse3.1241320                    |
0124128E | 8B E5                    | mov esp,ebp                              |
01241290 | 5D                       | pop ebp                                  |
01241291 | C3                       | ret                                      |
```

可以看到要得到flag，jne就不能跳，也就是`test eax,eax`后的ZF=1，也就是eax=0  
那这个eax=0从何而来？我们接着往上看  
```asm
012411A0 | 55                       | push ebp                                 |
012411A1 | 8B EC                    | mov ebp,esp                              |
012411A3 | 83 EC 44                 | sub esp,44                               |
012411A6 | A1 04 30 24 01           | mov eax,dword ptr ds:[1243004]           | eax:"OTacMDMzMTI="
012411AB | 33 C5                    | xor eax,ebp                              |
012411AD | 89 45 FC                 | mov dword ptr ss:[ebp-4],eax             |
012411B0 | 0F 57 C0                 | xorps xmm0,xmm0                          |
012411B3 | C7 45 F8 00 00 00 00     | mov dword ptr ss:[ebp-8],0               |
012411BA | 68 58 21 24 01           | push reverse3.1242158                    | 1242158:"Please enter the flag:"
012411BF | 0F 11 45 E8              | movups xmmword ptr ss:[ebp-18],xmm0      |
012411C3 | 0F 11 45 C0              | movups xmmword ptr ss:[ebp-40],xmm0      |
012411C7 | 0F 11 45 D0              | movups xmmword ptr ss:[ebp-30],xmm0      |
012411CB | 66 0F D6 45 E0           | movq qword ptr ss:[ebp-20],xmm0          |
012411D0 | E8 1B 01 00 00           | call reverse3.12412F0                    |
012411D5 | 8D 45 E8                 | lea eax,dword ptr ss:[ebp-18]            |
012411D8 | 50                       | push eax                                 | eax:"OTacMDMzMTI="
012411D9 | 68 70 21 24 01           | push reverse3.1242170                    | 1242170:"%20s"
012411DE | E8 CD 00 00 00           | call reverse3.12412B0                    |
012411E3 | 8D 4D E8                 | lea ecx,dword ptr ss:[ebp-18]            | 你的输入 -> ecx
012411E6 | 83 C4 0C                 | add esp,C                                |
012411E9 | 8D 51 01                 | lea edx,dword ptr ds:[ecx+1]             | 你的输入减第一个字节 -> edx
012411EC | 0F 1F 40 00              | nop dword ptr ds:[eax]                   | eax:"OTacMDMzMTI="
012411F0 | 8A 01                    | mov al,byte ptr ds:[ecx]                 |
012411F2 | 41                       | inc ecx                                  |
012411F3 | 84 C0                    | test al,al                               |
012411F5 | 75 F9                    | jne reverse3.12411F0                     |
012411F7 | 2B CA                    | sub ecx,edx                              | 你的输入的长度 -> ecx
012411F9 | 8D 55 E8                 | lea edx,dword ptr ss:[ebp-18]            | 输入 -> edx
012411FC | 56                       | push esi                                 | esi:"TacMDMzMTI="
012411FD | 51                       | push ecx                                 |
012411FE | 51                       | push ecx                                 |
012411FF | 8D 4D C0                 | lea ecx,dword ptr ss:[ebp-40]            |
01241202 | E8 F9 FD FF FF           | call reverse3.1241000                    | base64(你的输入) -> [ebp - 0x40]
01241207 | 8D 4D C0                 | lea ecx,dword ptr ss:[ebp-40]            |
0124120A | 83 C4 08                 | add esp,8                                |
0124120D | 33 D2                    | xor edx,edx                              |
0124120F | 8D 71 01                 | lea esi,dword ptr ds:[ecx+1]             | esi:"TacMDMzMTI="
01241212 | 8A 01                    | mov al,byte ptr ds:[ecx]                 |
01241214 | 41                       | inc ecx                                  |
01241215 | 84 C0                    | test al,al                               |
01241217 | 75 F9                    | jne reverse3.1241212                     |
01241219 | 2B CE                    | sub ecx,esi                              | 长度（base64你的输入） -> ecx
0124121B | 74 37                    | je reverse3.1241254                      |
0124121D | 0F 1F 00                 | nop dword ptr ds:[eax]                   | eax:"OTacMDMzMTI="
01241220 | 8A 4C 15 C0              | mov cl,byte ptr ss:[ebp+edx-40]          |
01241224 | 33 C0                    | xor eax,eax                              | eax:"OTacMDMzMTI="
01241226 | 3A 88 08 21 24 01        | cmp cl,byte ptr ds:[eax+1242108]         |
0124122C | 74 08                    | je reverse3.1241236                      |
0124122E | 40                       | inc eax                                  | eax:"OTacMDMzMTI="
0124122F | 83 F8 1A                 | cmp eax,1A                               | eax:"OTacMDMzMTI="
01241232 | 72 F2                    | jb reverse3.1241226                      |
01241234 | EB 0A                    | jmp reverse3.1241240                     |
01241236 | 8A 80 24 21 24 01        | mov al,byte ptr ds:[eax+1242124]         |
0124123C | 88 44 15 C0              | mov byte ptr ss:[ebp+edx-40],al          |
01241240 | 8D 4D C0                 | lea ecx,dword ptr ss:[ebp-40]            |
01241243 | 42                       | inc edx                                  |
01241244 | 8D 71 01                 | lea esi,dword ptr ds:[ecx+1]             | esi:"TacMDMzMTI="
01241247 | 8A 01                    | mov al,byte ptr ds:[ecx]                 |
01241249 | 41                       | inc ecx                                  |
0124124A | 84 C0                    | test al,al                               |
0124124C | 75 F9                    | jne reverse3.1241247                     |
0124124E | 2B CE                    | sub ecx,esi                              | esi:"TacMDMzMTI="
01241250 | 3B D1                    | cmp edx,ecx                              |
01241252 | 72 CC                    | jb reverse3.1241220                      |
01241254 | 6A 14                    | push 14                                  |
01241256 | 8D 45 C0                 | lea eax,dword ptr ss:[ebp-40]            |
01241259 | 68 40 21 24 01           | push reverse3.1242140                    | 1242140:"o2Ffx3V0OjJtYW5spQ=="
0124125E | 50                       | push eax                                 | eax:"OTacMDMzMTI="
0124125F | FF 15 C4 20 24 01        | call dword ptr ds:[<&strncmp>]           | 经过处理的base64与内置base64值比较，相等=>eax=0
01241265 | 83 C4 0C                 | add esp,C                                |
01241268 | 5E                       | pop esi                                  | esi:"TacMDMzMTI="
01241269 | 85 C0                    | test eax,eax                             | zf=1 => eax=0
0124126B | 75 07                    | jne reverse3.1241274                     |
0124126D | 68 78 21 24 01           | push reverse3.1242178                    | 1242178:"this is the right flag"
01241272 | EB 05                    | jmp reverse3.1241279                     |
01241274 | 68 90 21 24 01           | push reverse3.1242190                    | 1242190:"wrong input"
01241279 | FF 15 B0 20 24 01        | call dword ptr ds:[<&puts>]              |
0124127F | 8B 4D FC                 | mov ecx,dword ptr ss:[ebp-4]             |
01241282 | 83 C4 04                 | add esp,4                                |
01241285 | 33 CD                    | xor ecx,ebp                              |
01241287 | 33 C0                    | xor eax,eax                              | eax:"OTacMDMzMTI="
01241289 | E8 92 00 00 00           | call reverse3.1241320                    |
0124128E | 8B E5                    | mov esp,ebp                              |
01241290 | 5D                       | pop ebp                                  |
01241291 | C3                       | ret                                      |
```

看来是这几行做了手脚，压入了两个参数  
```asm
01241259 | 68 40 21 24 01           | push reverse3.1242140                    | 1242140:"o2Ffx3V0OjJtYW5spQ=="
0124125E | 50                       | push eax                                 | eax:"OTacMDMzMTI="
0124125F | FF 15 C4 20 24 01        | call dword ptr ds:[<&strncmp>]           | 经过处理的base64与内置base64值比较，相等=>eax=0
```

我们跟这个call进去看看
```asm
6C2F8C30 | 53                       | push ebx                                 |
6C2F8C31 | 56                       | push esi                                 | esi:"TacMDMzMTI="
6C2F8C32 | 8B 4C 24 0C              | mov ecx,dword ptr ss:[esp+C]             | 我们输入的base64变形后的值
6C2F8C36 | 8B 54 24 10              | mov edx,dword ptr ss:[esp+10]            | 内置base64值
6C2F8C3A | 8B 5C 24 14              | mov ebx,dword ptr ss:[esp+14]            |
6C2F8C3E | F7 C3 FF FF FF FF        | test ebx,FFFFFFFF                        |
6C2F8C44 | 74 50                    | je ucrtbase.6C2F8C96                     |
6C2F8C46 | 2B CA                    | sub ecx,edx                              |
6C2F8C48 | F7 C2 03 00 00 00        | test edx,3                               |
6C2F8C4E | 74 17                    | je ucrtbase.6C2F8C67                     |
6C2F8C50 | 0F B6 04 0A              | movzx eax,byte ptr ds:[edx+ecx]          | edx+ecx*1:"OTacMDMzMTI="
6C2F8C54 | 3A 02                    | cmp al,byte ptr ds:[edx]                 |
6C2F8C56 | 75 48                    | jne ucrtbase.6C2F8CA0                    |
6C2F8C58 | 85 C0                    | test eax,eax                             | eax:"OTacMDMzMTI="
6C2F8C5A | 74 3A                    | je ucrtbase.6C2F8C96                     |
6C2F8C5C | 42                       | inc edx                                  |
6C2F8C5D | 83 EB 01                 | sub ebx,1                                |
6C2F8C60 | 76 34                    | jbe ucrtbase.6C2F8C96                    |
6C2F8C62 | F6 C2 03                 | test dl,3                                |
6C2F8C65 | 75 E9                    | jne ucrtbase.6C2F8C50                    |
6C2F8C67 | 8D 04 0A                 | lea eax,dword ptr ds:[edx+ecx]           | eax:"OTacMDMzMTI="
6C2F8C6A | 25 FF 0F 00 00           | and eax,FFF                              | eax:"OTacMDMzMTI="
6C2F8C6F | 3D FC 0F 00 00           | cmp eax,FFC                              | eax:"OTacMDMzMTI="
6C2F8C74 | 77 DA                    | ja ucrtbase.6C2F8C50                     |
6C2F8C76 | 8B 04 0A                 | mov eax,dword ptr ds:[edx+ecx]           | eax:"OTacMDMzMTI="
6C2F8C79 | 3B 02                    | cmp eax,dword ptr ds:[edx]               | eax:"OTacMDMzMTI="
6C2F8C7B | 75 D3                    | jne ucrtbase.6C2F8C50                    |
6C2F8C7D | 83 EB 04                 | sub ebx,4                                |
6C2F8C80 | 76 14                    | jbe ucrtbase.6C2F8C96                    |
6C2F8C82 | 8D B0 FF FE FE FE        | lea esi,dword ptr ds:[eax-1010101]       | esi:"TacMDMzMTI="
6C2F8C88 | 83 C2 04                 | add edx,4                                |
6C2F8C8B | F7 D0                    | not eax                                  | eax:"OTacMDMzMTI="
6C2F8C8D | 23 C6                    | and eax,esi                              | eax:"OTacMDMzMTI=", esi:"TacMDMzMTI="
6C2F8C8F | A9 80 80 80 80           | test eax,80808080                        | eax:"OTacMDMzMTI="
6C2F8C94 | 74 D1                    | je ucrtbase.6C2F8C67                     |
6C2F8C96 | 33 C0                    | xor eax,eax                              | eax:"OTacMDMzMTI="
6C2F8C98 | 5E                       | pop esi                                  | esi:"TacMDMzMTI="
6C2F8C99 | 5B                       | pop ebx                                  |
6C2F8C9A | C3                       | ret                                      |
6C2F8C9B | EB 03                    | jmp ucrtbase.6C2F8CA0                    |
6C2F8C9D | CC                       | int3                                     |
6C2F8C9E | CC                       | int3                                     |
6C2F8C9F | CC                       | int3                                     |
6C2F8CA0 | 1B C0                    | sbb eax,eax                              | eax:"OTacMDMzMTI="
6C2F8CA2 | 83 C8 01                 | or eax,1                                 | eax:"OTacMDMzMTI="
6C2F8CA5 | 5E                       | pop esi                                  | esi:"TacMDMzMTI="
6C2F8CA6 | 5B                       | pop ebx                                  |
6C2F8CA7 | C3                       | ret                                      |
```

这段代码的跳转比较复杂，我们主要看这段  
```asm
6C2F8C50 | 0F B6 04 0A              | movzx eax,byte ptr ds:[edx+ecx]          | edx+ecx*1:"OTacMDMzMTI="
6C2F8C54 | 3A 02                    | cmp al,byte ptr ds:[edx]                 | edx:"o2Ffx3V0OjJtYW5spQ=="
6C2F8C56 | 75 48                    | jne ucrtbase.6C2F8CA0                    |
6C2F8C58 | 85 C0                    | test eax,eax                             |
6C2F8C5A | 74 3A                    | je ucrtbase.6C2F8C96                     |
6C2F8C5C | 42                       | inc edx                                  | edx:"o2Ffx3V0OjJtYW5spQ=="
6C2F8C5D | 83 EB 01                 | sub ebx,1                                |
6C2F8C60 | 76 34                    | jbe ucrtbase.6C2F8C96                    |
6C2F8C62 | F6 C2 03                 | test dl,3                                |
6C2F8C65 | 75 E9                    | jne ucrtbase.6C2F8C50                    |
6C2F8C67 | 8D 04 0A                 | lea eax,dword ptr ds:[edx+ecx]           | edx+ecx*1:"OTacMDMzMTI="
6C2F8C6A | 25 FF 0F 00 00           | and eax,FFF                              |
6C2F8C6F | 3D FC 0F 00 00           | cmp eax,FFC                              |
6C2F8C74 | 77 DA                    | ja ucrtbase.6C2F8C50                     |
```

通读可以发现就是把我们输入的base64变形后的值(`OTacMDMzMTI=`)按字节取出来一一和内置的`o2Ffx3V0OjJtYW5spQ==`做比较，只有当全部相等才跳到这把eax置零  
```asm
6C2F8C96 | 33 C0                    | xor eax,eax                              
```

然后退出函数

那么这个`OTacMDMzMTI=`是个什么呢？看着是个base64，但是我们解出来是`96?3312`，完全不是我们输入的`97103012`了，这个只怎么来的呢？我们继续看这段  
```asm
012411FF | 8D 4D C0                 | lea ecx,dword ptr ss:[ebp-40]            |
01241202 | E8 F9 FD FF FF           | call reverse3.1241000                    |base64(你的输入) -> [ebp - 0x40]
01241207 | 8D 4D C0                 | lea ecx,dword ptr ss:[ebp-40]            |
0124120A | 83 C4 08                 | add esp,8                                |
0124120D | 33 D2                    | xor edx,edx                              | edx:"97103012"
0124120F | 8D 71 01                 | lea esi,dword ptr ds:[ecx+1]             |
01241212 | 8A 01                    | mov al,byte ptr ds:[ecx]                 |
01241214 | 41                       | inc ecx                                  |
01241215 | 84 C0                    | test al,al                               |
01241217 | 75 F9                    | jne reverse3.1241212                     |
01241219 | 2B CE                    | sub ecx,esi                              | 长度（base64你的输入） -> ecx
0124121B | 74 37                    | je reverse3.1241254                      |
0124121D | 0F 1F 00                 | nop dword ptr ds:[eax]                   |
01241220 | 8A 4C 15 C0              | mov cl,byte ptr ss:[ebp+edx-40]          |
01241224 | 33 C0                    | xor eax,eax                              |
01241226 | 3A 88 08 21 24 01        | cmp cl,byte ptr ds:[eax+1242108]         | eax+1242108:"abcdefghijklmnopqrstuvwxyz"
0124122C | 74 08                    | je reverse3.1241236                      |
0124122E | 40                       | inc eax                                  |
0124122F | 83 F8 1A                 | cmp eax,1A                               |
01241232 | 72 F2                    | jb reverse3.1241226                      |
01241234 | EB 0A                    | jmp reverse3.1241240                     |
01241236 | 8A 80 24 21 24 01        | mov al,byte ptr ds:[eax+1242124]         | eax+1242124:"wxabopdefghijklqrstuvyzcmn"
0124123C | 88 44 15 C0              | mov byte ptr ss:[ebp+edx-40],al          |
01241240 | 8D 4D C0                 | lea ecx,dword ptr ss:[ebp-40]            |
01241243 | 42                       | inc edx                                  | edx:"97103012"
01241244 | 8D 71 01                 | lea esi,dword ptr ds:[ecx+1]             |
01241247 | 8A 01                    | mov al,byte ptr ds:[ecx]                 |
01241249 | 41                       | inc ecx                                  |
0124124A | 84 C0                    | test al,al                               |
0124124C | 75 F9                    | jne reverse3.1241247                     |
0124124E | 2B CE                    | sub ecx,esi                              |
01241250 | 3B D1                    | cmp edx,ecx                              | edx:"97103012"
01241252 | 72 CC                    | jb reverse3.1241220                      |
01241254 | 6A 14                    | push 14                                  |
```

下面这行代码有兴趣的可以跟进去看看，其实就是base64编码，苦逼的我傻乎乎地跟完了  
```asm
01241202 | E8 F9 FD FF FF           | call reverse3.1241000                    |base64(你的输入) -> [ebp - 0x40]
```

那`97103012`的base64是`OTcxMDMwMTI=`呀，这个`OTacMDMzMTI=`是怎么来的呢？我们看着一段  
```asm
01241220 | 8A 4C 15 C0              | mov cl,byte ptr ss:[ebp+edx-40]          |
01241224 | 33 C0                    | xor eax,eax                              |
01241226 | 3A 88 08 21 24 01        | cmp cl,byte ptr ds:[eax+1242108]         | eax+1242108:"abcdefghijklmnopqrstuvwxyz"
0124122C | 74 08                    | je reverse3.1241236                      |
0124122E | 40                       | inc eax                                  |
0124122F | 83 F8 1A                 | cmp eax,1A                               |
01241232 | 72 F2                    | jb reverse3.1241226                      |
01241234 | EB 0A                    | jmp reverse3.1241240                     |
01241236 | 8A 80 24 21 24 01        | mov al,byte ptr ds:[eax+1242124]         | eax+1242124:"wxabopdefghijklqrstuvyzcmn"
0124123C | 88 44 15 C0              | mov byte ptr ss:[ebp+edx-40],al          |
01241240 | 8D 4D C0                 | lea ecx,dword ptr ss:[ebp-40]            |
01241243 | 42                       | inc edx                                  | edx:"97103012"
01241244 | 8D 71 01                 | lea esi,dword ptr ds:[ecx+1]             |
01241247 | 8A 01                    | mov al,byte ptr ds:[ecx]                 |
01241249 | 41                       | inc ecx                                  |
0124124A | 84 C0                    | test al,al                               |
0124124C | 75 F9                    | jne reverse3.1241247                     |
0124124E | 2B CE                    | sub ecx,esi                              |
01241250 | 3B D1                    | cmp edx,ecx                              | edx:"97103012"
01241252 | 72 CC                    | jb reverse3.1241220                      |
```
这一段的工作大家跟跟就知道，就是通过一次次循环将`OTcxMDMwMTI=`中的值通过下面这个对应关系一一置换  
```
abcdefghijklmnopqrstuvwxyz
wxabopdefghijklqrstuvyzcmn
```
所以`OTcxMDMwMTI=`变成了`OTacMDMzMTI=`  

好的，我们看到了这里，相信已经知道密码是什么了，也就是我们变形后的base64值要等于`o2Ffx3V0OjJtYW5spQ==`  
那就倒着置换呗，得出来正确的base64是`e2Fib3V0OmJsYW5rfQ==`，解码为`{about:blank}`

![](http://ww1.sinaimg.cn/large/c0264382gy1fjpd8arx26j20og0ge0tq.jpg)

[例子CM](https://pan.baidu.com/s/1mi63WXM)
