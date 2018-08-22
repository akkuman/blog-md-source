title: 160CrackMe练手 002
date: 2017-09-16 14:29:51
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



首先查壳无壳，输入伪码报错，根据报错od查找字符串，定位到错误代码附近，可以看到有个条件跳转，改掉就可以爆破，接下来分析下注册算法，我们周围看看，从最近几个call看，并没有我们输入的用户名在堆栈中出现，那我们直接从这个函数开头往下找，一般一个函数开头是push ebp一段代码用来提升堆栈，找到后我们往下找，注意堆栈，直到我们输入的字符出现，开始细心往下跟  
<!--more-->

```asm
00402310   > \55            push ebp
00402311   .  8BEC          mov ebp,esp
00402313   .  83EC 0C       sub esp,0xC
00402316   .  68 26104000   push <jmp.&MSVBVM50.__vbaExceptHandler>  ;  SE 处理程序安装
0040231B   .  64:A1 0000000>mov eax,dword ptr fs:[0]
00402321   .  50            push eax                                 ;  Afkayas_.00402191
00402322   .  64:8925 00000>mov dword ptr fs:[0],esp
00402329   .  81EC B0000000 sub esp,0xB0
0040232F   .  53            push ebx
00402330   .  56            push esi
00402331   .  8B75 08       mov esi,dword ptr ss:[ebp+0x8]           ;  esi -> ASCII "2@"
00402334   .  57            push edi
00402335   .  8BC6          mov eax,esi                              ;  eax -> ASCII "2@"
00402337   .  83E6 FE       and esi,-0x2
0040233A   .  8965 F4       mov dword ptr ss:[ebp-0xC],esp
0040233D   .  83E0 01       and eax,0x1
00402340   .  8B1E          mov ebx,dword ptr ds:[esi]
00402342   .  C745 F8 08104>mov dword ptr ss:[ebp-0x8],Afkayas_.0040>
00402349   .  56            push esi
0040234A   .  8945 FC       mov dword ptr ss:[ebp-0x4],eax           ;  Afkayas_.00402191
0040234D   .  8975 08       mov dword ptr ss:[ebp+0x8],esi
00402350   .  FF53 04       call dword ptr ds:[ebx+0x4]              ;  msvbvm50.7404C5C8
00402353   .  8B83 10030000 mov eax,dword ptr ds:[ebx+0x310]
00402359   .  33FF          xor edi,edi
0040235B   .  56            push esi
0040235C   .  897D E8       mov dword ptr ss:[ebp-0x18],edi
0040235F   .  897D E4       mov dword ptr ss:[ebp-0x1C],edi
00402362   .  897D E0       mov dword ptr ss:[ebp-0x20],edi
00402365   .  897D DC       mov dword ptr ss:[ebp-0x24],edi
00402368   .  897D D8       mov dword ptr ss:[ebp-0x28],edi
0040236B   .  897D D4       mov dword ptr ss:[ebp-0x2C],edi
0040236E   .  897D C4       mov dword ptr ss:[ebp-0x3C],edi
00402371   .  897D B4       mov dword ptr ss:[ebp-0x4C],edi
00402374   .  897D A4       mov dword ptr ss:[ebp-0x5C],edi
00402377   .  897D 94       mov dword ptr ss:[ebp-0x6C],edi
0040237A   .  8985 40FFFFFF mov dword ptr ss:[ebp-0xC0],eax          ;  Afkayas_.00402191
00402380   .  FFD0          call eax                                 ;  Afkayas_.00402191
00402382   .  8D4D D4       lea ecx,dword ptr ss:[ebp-0x2C]
00402385   .  50            push eax                                 ;  Afkayas_.00402191
00402386   .  51            push ecx
00402387   .  FF15 0C414000 call dword ptr ds:[<&MSVBVM50.__vbaObjSe>;  msvbvm50.__vbaObjSet
0040238D   .  8B9B 00030000 mov ebx,dword ptr ds:[ebx+0x300]
00402393   .  56            push esi
00402394   .  8985 50FFFFFF mov dword ptr ss:[ebp-0xB0],eax          ;  Afkayas_.00402191
0040239A   .  899D 3CFFFFFF mov dword ptr ss:[ebp-0xC4],ebx
004023A0   .  FFD3          call ebx
004023A2   .  8D55 DC       lea edx,dword ptr ss:[ebp-0x24]
004023A5   .  50            push eax                                 ;  Afkayas_.00402191
004023A6   .  52            push edx
004023A7   .  FF15 0C414000 call dword ptr ds:[<&MSVBVM50.__vbaObjSe>;  msvbvm50.__vbaObjSet
004023AD   .  8BD8          mov ebx,eax                              ;  Afkayas_.00402191
004023AF   .  8D4D E8       lea ecx,dword ptr ss:[ebp-0x18]
004023B2   .  51            push ecx
004023B3   .  53            push ebx
004023B4   .  8B03          mov eax,dword ptr ds:[ebx]
004023B6   .  FF90 A0000000 call dword ptr ds:[eax+0xA0]
004023BC   .  3BC7          cmp eax,edi
004023BE   .  7D 12         jge short Afkayas_.004023D2
004023C0   .  68 A0000000   push 0xA0
004023C5   .  68 5C1B4000   push Afkayas_.00401B5C
004023CA   .  53            push ebx
004023CB   .  50            push eax                                 ;  Afkayas_.00402191
004023CC   .  FF15 04414000 call dword ptr ds:[<&MSVBVM50.__vbaHresu>;  msvbvm50.__vbaHresultCheckObj
004023D2   >  56            push esi
004023D3   .  FF95 3CFFFFFF call dword ptr ss:[ebp-0xC4]
004023D9   .  8D55 D8       lea edx,dword ptr ss:[ebp-0x28]
004023DC   .  50            push eax                                 ;  Afkayas_.00402191
004023DD   .  52            push edx
004023DE   .  FF15 0C414000 call dword ptr ds:[<&MSVBVM50.__vbaObjSe>;  msvbvm50.__vbaObjSet
004023E4   .  8BD8          mov ebx,eax                              ;  Afkayas_.00402191
004023E6   .  8D4D E4       lea ecx,dword ptr ss:[ebp-0x1C]
004023E9   .  51            push ecx
004023EA   .  53            push ebx
004023EB   .  8B03          mov eax,dword ptr ds:[ebx]
004023ED   .  FF90 A0000000 call dword ptr ds:[eax+0xA0]
004023F3   .  3BC7          cmp eax,edi
004023F5   .  7D 12         jge short Afkayas_.00402409
004023F7   .  68 A0000000   push 0xA0
004023FC   .  68 5C1B4000   push Afkayas_.00401B5C
00402401   .  53            push ebx
00402402   .  50            push eax                                 ;  Afkayas_.00402191
00402403   .  FF15 04414000 call dword ptr ds:[<&MSVBVM50.__vbaHresu>;  msvbvm50.__vbaHresultCheckObj
00402409   >  8B95 50FFFFFF mov edx,dword ptr ss:[ebp-0xB0]
0040240F   .  8B45 E4       mov eax,dword ptr ss:[ebp-0x1C]          ;  用户名 -> eax
00402412   .  50            push eax                                 ; /用户名 -> 堆栈
00402413   .  8B1A          mov ebx,dword ptr ds:[edx]               ; |
00402415   .  FF15 E4404000 call dword ptr ds:[<&MSVBVM50.__vbaLenBs>; \len(用户名) -> eax
0040241B   .  8BF8          mov edi,eax                              ;  len(用户名) -> edi
0040241D   .  8B4D E8       mov ecx,dword ptr ss:[ebp-0x18]          ;  用户名 -> ecx
00402420   .  69FF FB7C0100 imul edi,edi,0x17CFB                     ;  len(用户名) * 0x17CFB ==> edi=A6ADD
00402426   .  51            push ecx                                 ; /String = NULL
00402427   .  0F80 91020000 jo Afkayas_.004026BE                     ; |
0040242D   .  FF15 F8404000 call dword ptr ds:[<&MSVBVM50.#rtcAnsiVa>; \用户名去掉首字母 -> edx
00402433   .  0FBFD0        movsx edx,ax
00402436   .  03FA          add edi,edx
00402438   .  0F80 80020000 jo Afkayas_.004026BE
0040243E   .  57            push edi                                 ;  len(用户名) * 0x17CFB 入栈-> ebp-D4
0040243F   .  FF15 E0404000 call dword ptr ds:[<&MSVBVM50.__vbaStrI4>;  十进制(len(用户名) * 0x17CFB) + 首字母十进制ascii值  -> eax
00402445   .  8BD0          mov edx,eax                              ;  Afkayas_.00402191
00402447   .  8D4D E0       lea ecx,dword ptr ss:[ebp-0x20]
0040244A   .  FF15 70414000 call dword ptr ds:[<&MSVBVM50.__vbaStrMo>;  msvbvm50.__vbaStrMove
00402450   .  8BBD 50FFFFFF mov edi,dword ptr ss:[ebp-0xB0]
00402456   .  50            push eax                                 ;  Afkayas_.00402191
00402457   .  57            push edi                                 ;  十进制(len(用户名) * 0x17CFB) + 首字母十进制ascii值  入栈  -> ebp-D4
00402458   .  FF93 A4000000 call dword ptr ds:[ebx+0xA4]
0040245E   .  85C0          test eax,eax                             ;  Afkayas_.00402191
00402460   .  7D 12         jge short Afkayas_.00402474
00402462   .  68 A4000000   push 0xA4
00402467   .  68 5C1B4000   push Afkayas_.00401B5C
0040246C   .  57            push edi
0040246D   .  50            push eax                                 ;  Afkayas_.00402191
0040246E   .  FF15 04414000 call dword ptr ds:[<&MSVBVM50.__vbaHresu>;  msvbvm50.__vbaHresultCheckObj
00402474   >  8D45 E0       lea eax,dword ptr ss:[ebp-0x20]
00402477   .  8D4D E4       lea ecx,dword ptr ss:[ebp-0x1C]
0040247A   .  50            push eax                                 ;  Afkayas_.00402191
0040247B   .  8D55 E8       lea edx,dword ptr ss:[ebp-0x18]
0040247E   .  51            push ecx
0040247F   .  52            push edx
00402480   .  6A 03         push 0x3
00402482   .  FF15 5C414000 call dword ptr ds:[<&MSVBVM50.__vbaFreeS>;  msvbvm50.__vbaFreeStrList
00402488   .  83C4 10       add esp,0x10
0040248B   .  8D45 D4       lea eax,dword ptr ss:[ebp-0x2C]
0040248E   .  8D4D D8       lea ecx,dword ptr ss:[ebp-0x28]
00402491   .  8D55 DC       lea edx,dword ptr ss:[ebp-0x24]
00402494   .  50            push eax                                 ;  Afkayas_.00402191
00402495   .  51            push ecx
00402496   .  52            push edx
00402497   .  6A 03         push 0x3
00402499   .  FF15 F4404000 call dword ptr ds:[<&MSVBVM50.__vbaFreeO>;  msvbvm50.__vbaFreeObjList
0040249F   .  8B06          mov eax,dword ptr ds:[esi]
004024A1   .  83C4 10       add esp,0x10
004024A4   .  56            push esi
004024A5   .  FF90 04030000 call dword ptr ds:[eax+0x304]
004024AB   .  8B1D 0C414000 mov ebx,dword ptr ds:[<&MSVBVM50.__vbaOb>;  msvbvm50.__vbaObjSet
004024B1   .  50            push eax                                 ;  Afkayas_.00402191
004024B2   .  8D45 DC       lea eax,dword ptr ss:[ebp-0x24]
004024B5   .  50            push eax                                 ;  Afkayas_.00402191
004024B6   .  FFD3          call ebx                                 ;  <&MSVBVM50.__vbaObjSet>
004024B8   .  8BF8          mov edi,eax                              ;  Afkayas_.00402191
004024BA   .  8D55 E8       lea edx,dword ptr ss:[ebp-0x18]
004024BD   .  52            push edx
004024BE   .  57            push edi
004024BF   .  8B0F          mov ecx,dword ptr ds:[edi]
004024C1   .  FF91 A0000000 call dword ptr ds:[ecx+0xA0]
004024C7   .  85C0          test eax,eax                             ;  Afkayas_.00402191
004024C9   .  7D 12         jge short Afkayas_.004024DD
004024CB   .  68 A0000000   push 0xA0
004024D0   .  68 5C1B4000   push Afkayas_.00401B5C
004024D5   .  57            push edi
004024D6   .  50            push eax                                 ;  Afkayas_.00402191
004024D7   .  FF15 04414000 call dword ptr ds:[<&MSVBVM50.__vbaHresu>;  msvbvm50.__vbaHresultCheckObj
004024DD   >  56            push esi
004024DE   .  FF95 40FFFFFF call dword ptr ss:[ebp-0xC0]
004024E4   .  50            push eax                                 ;  Afkayas_.00402191
004024E5   .  8D45 D8       lea eax,dword ptr ss:[ebp-0x28]
004024E8   .  50            push eax                                 ;  Afkayas_.00402191
004024E9   .  FFD3          call ebx
004024EB   .  8BF0          mov esi,eax                              ;  Afkayas_.00402191
004024ED   .  8D55 E4       lea edx,dword ptr ss:[ebp-0x1C]
004024F0   .  52            push edx
004024F1   .  56            push esi
004024F2   .  8B0E          mov ecx,dword ptr ds:[esi]
004024F4   .  FF91 A0000000 call dword ptr ds:[ecx+0xA0]
004024FA   .  85C0          test eax,eax                             ;  Afkayas_.00402191
004024FC   .  7D 12         jge short Afkayas_.00402510
004024FE   .  68 A0000000   push 0xA0
00402503   .  68 5C1B4000   push Afkayas_.00401B5C
00402508   .  56            push esi
00402509   .  50            push eax                                 ;  Afkayas_.00402191
0040250A   .  FF15 04414000 call dword ptr ds:[<&MSVBVM50.__vbaHresu>;  msvbvm50.__vbaHresultCheckObj
00402510   >  8B45 E8       mov eax,dword ptr ss:[ebp-0x18]          ;  user32.77D2BBF7
00402513   .  8B4D E4       mov ecx,dword ptr ss:[ebp-0x1C]
00402516   .  8B3D 00414000 mov edi,dword ptr ds:[<&MSVBVM50.__vbaSt>;  十进制(len(用户名) * 0x17CFB) + 首字母十进制ascii值 -> ecx       密码 -> eax
0040251C   .  50            push eax                                 ;  Afkayas_.00402191
0040251D   .  68 701B4000   push Afkayas_.00401B70                   ;  "AKA-"入栈
00402522   .  51            push ecx                                 ; /十进制(len(用户名) * 0x17CFB) + 首字母十进制ascii值 入栈
00402523   .  FFD7          call edi                                 ; \"AKA-"+"十进制(len(用户名) * 0x17CFB) + 首字母十进制ascii值"   ->  eax
00402525   .  8B1D 70414000 mov ebx,dword ptr ds:[<&MSVBVM50.__vbaSt>;  msvbvm50.__vbaStrMove
0040252B   .  8BD0          mov edx,eax                              ;  Afkayas_.00402191
0040252D   .  8D4D E0       lea ecx,dword ptr ss:[ebp-0x20]
00402530   .  FFD3          call ebx                                 ;  <&MSVBVM50.__vbaStrMove>
00402532   .  50            push eax                                 ;  Afkayas_.00402191
00402533   .  FF15 28414000 call dword ptr ds:[<&MSVBVM50.__vbaStrCm>;  msvbvm50.__vbaStrCmp
00402539   .  8BF0          mov esi,eax                              ;  Afkayas_.00402191
0040253B   .  8D55 E0       lea edx,dword ptr ss:[ebp-0x20]
0040253E   .  F7DE          neg esi
00402540   .  8D45 E8       lea eax,dword ptr ss:[ebp-0x18]
00402543   .  52            push edx
00402544   .  1BF6          sbb esi,esi
00402546   .  8D4D E4       lea ecx,dword ptr ss:[ebp-0x1C]
00402549   .  50            push eax                                 ;  Afkayas_.00402191
0040254A   .  46            inc esi
0040254B   .  51            push ecx
0040254C   .  6A 03         push 0x3
0040254E   .  F7DE          neg esi
00402550   .  FF15 5C414000 call dword ptr ds:[<&MSVBVM50.__vbaFreeS>;  msvbvm50.__vbaFreeStrList
00402556   .  83C4 10       add esp,0x10
00402559   .  8D55 D8       lea edx,dword ptr ss:[ebp-0x28]
0040255C   .  8D45 DC       lea eax,dword ptr ss:[ebp-0x24]
0040255F   .  52            push edx
00402560   .  50            push eax                                 ;  Afkayas_.00402191
00402561   .  6A 02         push 0x2
00402563   .  FF15 F4404000 call dword ptr ds:[<&MSVBVM50.__vbaFreeO>;  msvbvm50.__vbaFreeObjList
00402569   .  83C4 0C       add esp,0xC
0040256C   .  B9 04000280   mov ecx,0x80020004
00402571   .  B8 0A000000   mov eax,0xA
00402576   .  894D 9C       mov dword ptr ss:[ebp-0x64],ecx
00402579   .  66:85F6       test si,si
0040257C   .  8945 94       mov dword ptr ss:[ebp-0x6C],eax          ;  Afkayas_.00402191
0040257F   .  894D AC       mov dword ptr ss:[ebp-0x54],ecx
00402582   .  8945 A4       mov dword ptr ss:[ebp-0x5C],eax          ;  Afkayas_.00402191
00402585   .  894D BC       mov dword ptr ss:[ebp-0x44],ecx
00402588   .  8945 B4       mov dword ptr ss:[ebp-0x4C],eax          ;  Afkayas_.00402191
0040258B   .  74 58         je short Afkayas_.004025E5
0040258D   .  68 801B4000   push Afkayas_.00401B80                   ;  You Get It
00402592   .  68 9C1B4000   push Afkayas_.00401B9C                   ;  \r\n
00402597   .  FFD7          call edi
00402599   .  8BD0          mov edx,eax                              ;  Afkayas_.00402191
0040259B   .  8D4D E8       lea ecx,dword ptr ss:[ebp-0x18]
0040259E   .  FFD3          call ebx
004025A0   .  50            push eax                                 ;  Afkayas_.00402191
004025A1   .  68 A81B4000   push Afkayas_.00401BA8                   ;  KeyGen It Now
004025A6   .  FFD7          call edi
004025A8   .  8D4D 94       lea ecx,dword ptr ss:[ebp-0x6C]
004025AB   .  8945 CC       mov dword ptr ss:[ebp-0x34],eax          ;  Afkayas_.00402191
004025AE   .  8D55 A4       lea edx,dword ptr ss:[ebp-0x5C]
004025B1   .  51            push ecx
004025B2   .  8D45 B4       lea eax,dword ptr ss:[ebp-0x4C]
004025B5   .  52            push edx
004025B6   .  50            push eax                                 ;  Afkayas_.00402191
004025B7   .  8D4D C4       lea ecx,dword ptr ss:[ebp-0x3C]
004025BA   .  6A 00         push 0x0
004025BC   .  51            push ecx
004025BD   .  C745 C4 08000>mov dword ptr ss:[ebp-0x3C],0x8
004025C4   .  FF15 10414000 call dword ptr ds:[<&MSVBVM50.#rtcMsgBox>;  msvbvm50.rtcMsgBox
004025CA   .  8D4D E8       lea ecx,dword ptr ss:[ebp-0x18]
004025CD   .  FF15 80414000 call dword ptr ds:[<&MSVBVM50.__vbaFreeS>;  msvbvm50.__vbaFreeStr
004025D3   .  8D55 94       lea edx,dword ptr ss:[ebp-0x6C]
004025D6   .  8D45 A4       lea eax,dword ptr ss:[ebp-0x5C]
004025D9   .  52            push edx
004025DA   .  8D4D B4       lea ecx,dword ptr ss:[ebp-0x4C]
004025DD   .  50            push eax                                 ;  Afkayas_.00402191
004025DE   .  8D55 C4       lea edx,dword ptr ss:[ebp-0x3C]
004025E1   .  51            push ecx
004025E2   .  52            push edx
004025E3   .  EB 56         jmp short Afkayas_.0040263B
004025E5   >  68 C81B4000   push Afkayas_.00401BC8                   ;  You Get Wrong
004025EA   .  68 9C1B4000   push Afkayas_.00401B9C                   ;  \r\n
004025EF   .  FFD7          call edi
004025F1   .  8BD0          mov edx,eax                              ;  Afkayas_.00402191
004025F3   .  8D4D E8       lea ecx,dword ptr ss:[ebp-0x18]
004025F6   .  FFD3          call ebx
004025F8   .  50            push eax                                 ;  Afkayas_.00402191
004025F9   .  68 E81B4000   push Afkayas_.00401BE8                   ;  Try Again
```

通过分析可以看到加密关键代码是  
```asm
0040240F   .  8B45 E4       mov eax,dword ptr ss:[ebp-0x1C]          ;  用户名 -> eax
00402412   .  50            push eax                                 ; /用户名 -> 堆栈
00402413   .  8B1A          mov ebx,dword ptr ds:[edx]               ; |
00402415   .  FF15 E4404000 call dword ptr ds:[<&MSVBVM50.__vbaLenBs>; \len(用户名) -> eax
0040241B   .  8BF8          mov edi,eax                              ;  len(用户名) -> edi
0040241D   .  8B4D E8       mov ecx,dword ptr ss:[ebp-0x18]          ;  用户名 -> ecx
00402420   .  69FF FB7C0100 imul edi,edi,0x17CFB                     ;  len(用户名) * 0x17CFB ==> edi=A6ADD
00402426   .  51            push ecx                                 ; /String = NULL
00402427   .  0F80 91020000 jo Afkayas_.004026BE                     ; |
0040242D   .  FF15 F8404000 call dword ptr ds:[<&MSVBVM50.#rtcAnsiVa>; \用户名去掉首字母 -> edx
0040243E   .  57            push edi                                 ;  len(用户名) * 0x17CFB 入栈-> ebp-D4
0040243F   .  FF15 E0404000 call dword ptr ds:[<&MSVBVM50.__vbaStrI4>;  十进制(len(用户名) * 0x17CFB) + 首字母十进制ascii值  -> eax
0040251D   .  68 701B4000   push Afkayas_.00401B70                   ;  "AKA-"入栈
00402522   .  51            push ecx                                 ; /十进制(len(用户名) * 0x17CFB) + 首字母十进制ascii值 入栈
00402523   .  FFD7          call edi                                 ; \"AKA-"+"十进制(len(用户名) * 0x17CFB) + 首字母十进制ascii值"   ->  eax
```

可以看出流程就是 "AKA-"+"十进制(len(用户名) * 0x17CFB) + 首字母十进制ascii值"  

写注册机（Python）  
```python
import sys

username = sys.argv[1]
pwend = len(username) * 0x17CFB + ord(username[0])
password = "AKA-%d"%pwend
print(password)
```

测试结果  
```bash
C:\Users\Administrator\Desktop>python 1.py akkuman
AKA-682814
```

![](http://ww1.sinaimg.cn/large/c0264382gy1fjle318xy9j20ir0abgu6.jpg)
