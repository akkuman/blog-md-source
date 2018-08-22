title: 160CrackMe练手 001
date: 2017-09-15 16:16:12
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



peid判断无壳，打开输入伪码注册，根据报错od查找字符串  
接下来定位到字符串周边代码  
<!--more-->

```asm
0042FA15  |.  8D55 F0       lea edx,[local.4]
0042FA18  |.  8B83 DC010000 mov eax,dword ptr ds:[ebx+0x1DC]
0042FA1E  |.  E8 35B0FEFF   call Acid_bur.0041AA58
0042FA23  |.  8B45 F0       mov eax,[local.4]
0042FA26  |.  0FB640 03     movzx eax,byte ptr ds:[eax+0x3]
0042FA2A  |.  6BF0 0B       imul esi,eax,0xB
0042FA2D  |.  8D55 EC       lea edx,[local.5]
0042FA30  |.  8B83 DC010000 mov eax,dword ptr ds:[ebx+0x1DC]
0042FA36  |.  E8 1DB0FEFF   call Acid_bur.0041AA58
0042FA3B  |.  8B45 EC       mov eax,[local.5]                        ;  堆栈中可看到[local.5和4]都是你输入的用户名
0042FA3E  |.  0FB640 02     movzx eax,byte ptr ds:[eax+0x2]
0042FA42  |.  6BC0 0E       imul eax,eax,0xE
0042FA45  |.  03F0          add esi,eax
0042FA47  |.  8935 58174300 mov dword ptr ds:[0x431758],esi
0042FA4D  |.  A1 6C174300   mov eax,dword ptr ds:[0x43176C]
0042FA52  |.  E8 D96EFDFF   call Acid_bur.00406930
0042FA57  |.  83F8 04       cmp eax,0x4                              ;  如果用户名长度大于等于4跳转
0042FA5A  |.  7D 1D         jge short Acid_bur.0042FA79
0042FA5C  |.  6A 00         push 0x0
0042FA5E  |.  B9 74FB4200   mov ecx,Acid_bur.0042FB74                ;  Try Again!
0042FA63  |.  BA 80FB4200   mov edx,Acid_bur.0042FB80                ;  Sorry , The serial is incorect !
0042FA68  |.  A1 480A4300   mov eax,dword ptr ds:[0x430A48]
0042FA6D  |.  8B00          mov eax,dword ptr ds:[eax]
0042FA6F  |.  E8 FCA6FFFF   call Acid_bur.0042A170
0042FA74  |.  E9 BE000000   jmp Acid_bur.0042FB37
0042FA79  |>  8D55 F0       lea edx,[local.4]
0042FA7C  |.  8B83 DC010000 mov eax,dword ptr ds:[ebx+0x1DC]
0042FA82  |.  E8 D1AFFEFF   call Acid_bur.0041AA58
0042FA87  |.  8B45 F0       mov eax,[local.4]                        ;  取你输入的用户名
0042FA8A  |.  0FB600        movzx eax,byte ptr ds:[eax]              ;  取用户名的第一个字母放入eax
0042FA8D  |.  F72D 50174300 imul dword ptr ds:[0x431750]             ;  eax = eax * 29h
0042FA93  |.  A3 50174300   mov dword ptr ds:[0x431750],eax
0042FA98  |.  A1 50174300   mov eax,dword ptr ds:[0x431750]
0042FA9D  |.  0105 50174300 add dword ptr ds:[0x431750],eax          ;  [0x431750] = eax * 2
0042FAA3  |.  8D45 FC       lea eax,[local.1]
0042FAA6  |.  BA ACFB4200   mov edx,Acid_bur.0042FBAC                ;  CW
0042FAAB  |.  E8 583CFDFF   call Acid_bur.00403708                   ;  观察堆栈可发现"CW"放入了[local.1]
0042FAB0  |.  8D45 F8       lea eax,[local.2]
0042FAB3  |.  BA B8FB4200   mov edx,Acid_bur.0042FBB8                ;  CRACKED
0042FAB8  |.  E8 4B3CFDFF   call Acid_bur.00403708                   ;  观察堆栈可发现"CRACKED"放入了[local.2]
0042FABD  |.  FF75 FC       push [local.1]                           ;  Acid_bur.0042FBAC
0042FAC0  |.  68 C8FB4200   push Acid_bur.0042FBC8                   ;  -  ;两个push把"CW"和"-"入栈
0042FAC5  |.  8D55 E8       lea edx,[local.6]
0042FAC8  |.  A1 50174300   mov eax,dword ptr ds:[0x431750]
0042FACD  |.  E8 466CFDFF   call Acid_bur.00406718                   ;  用户名第一个字母*29*2的值放入[local.6]
0042FAD2  |.  FF75 E8       push [local.6]
0042FAD5  |.  68 C8FB4200   push Acid_bur.0042FBC8                   ;  -
0042FADA  |.  FF75 F8       push [local.2]                           ;  "用户名第一个字母*29*2","-","CRACKED"入栈
0042FADD  |.  8D45 F4       lea eax,[local.3]
0042FAE0  |.  BA 05000000   mov edx,0x5
0042FAE5  |.  E8 C23EFDFF   call Acid_bur.004039AC                   ;  CW-算好的数据-CRACKED  放入[local.3]
0042FAEA  |.  8D55 F0       lea edx,[local.4]
0042FAED  |.  8B83 E0010000 mov eax,dword ptr ds:[ebx+0x1E0]
0042FAF3  |.  E8 60AFFEFF   call Acid_bur.0041AA58
0042FAF8  |.  8B55 F0       mov edx,[local.4]                        ;  取出你输入的密码=>edx
0042FAFB  |.  8B45 F4       mov eax,[local.3]                        ;  正确密码=>eax
0042FAFE  |.  E8 F93EFDFF   call Acid_bur.004039FC
0042FB03  |.  75 1A         jnz short Acid_bur.0042FB1F              ;  判断密码是否正确
0042FB05  |.  6A 00         push 0x0
0042FB07  |.  B9 CCFB4200   mov ecx,Acid_bur.0042FBCC                ;  Congratz !!
0042FB0C  |.  BA D8FB4200   mov edx,Acid_bur.0042FBD8                ;  Good job dude =)
0042FB11  |.  A1 480A4300   mov eax,dword ptr ds:[0x430A48]
0042FB16  |.  8B00          mov eax,dword ptr ds:[eax]
0042FB18  |.  E8 53A6FFFF   call Acid_bur.0042A170
0042FB1D  |.  EB 18         jmp short Acid_bur.0042FB37
0042FB1F  |>  6A 00         push 0x0
0042FB21  |.  B9 74FB4200   mov ecx,Acid_bur.0042FB74                ;  Try Again!
0042FB26  |.  BA 80FB4200   mov edx,Acid_bur.0042FB80                ;  Sorry , The serial is incorect !
```

注册算法就是password = "CW-" + 取用户名第一位asciix29x2 + "-CRACKED"  

注册机（python）：  
```python
import sys

username = sys.argv[1]
password = ord(username[0])*0x29*0x2
print("password:"+"CW-"+"%d"%(password)+"-CRACKED")
```

测试：  
```bash
C:\Users\Administrator\Desktop>python 1.py akkuman
password:CW-7954-CRACKED
```

![](http://ww1.sinaimg.cn/large/c0264382gy1fjkbn9nb37j20di068407.jpg)
