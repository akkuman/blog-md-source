title: Windows环境下32位汇编语言程序设计笔记-基础篇
date: 2017-09-14 22:09:28
update: ""
author: me
tags:
- 读书笔记
- 二进制
categories:
- 读书笔记
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



## 内存模式
```asm
		.386
		.model flat,stdcall ;子程序调用模式，win32中只能用stdcall，因为win32api调用使用的这个
		option casemap:none ;定义了程序中变量和子程序名是否对大小写敏感，win32api名称区分大小写，所以只需要记住这个定式
```
<!--more-->
1. 指定使用的指令集
2. .model语句
```
.model 内存模式[,语言模式][,其他模式]
```

**内存模式**

模式 | 内存使用方式
-----|-------
tiny   | 用来建立.com文件，所有的代码、数据和堆栈都在同一个64KB段内
small | 建立代码和数据分别用一个64KB段的.exe文件
medium | 代码段可以有多个64KB段，数据段只有一个64KB段
compact | 代码段只有一个64KB段，数据段可以有多个64KB段
large  | 代码段和数据段都可以有多个64KB段
huge  | 同large，并且数据段中的一个数组也可以超过64KB
flat     | Win32程序使用的模式，代码和数据使用同一个4GB段

***对于Win32程序来说，只有一种内存模式，flat模式***

## 源程序结构
```asm
.386
.model flat,stdcall
option casemap:none
  <一些include语句>
.stack [堆栈段的大小] ;常忽略不写
.data
  <一些初始化过的变量定义>
.data?
  <一些没有初始化过的变量定义>
.const
  <一些常量定义>
.code
  <代码>
  <开始标号>
      <其他语句>
  end <开始标号>
```

## 局部变量的定义
```asm
local    变量名1[[重复数量]][:类型],变量名2[[重复数量]][:类型]......
```

*local伪指令必须紧接在子程序的伪指令proc后*

**变量的类型**

| 名称 | 表示方式 | 缩写 |
|:-----|:---------|:-----|
| 字节 | Byte | db |
| 字 | word | dw |
| 双字(doubleword) | dword | dd |
| 三字(farword) | fword | df |
| 四字(quadword) | qword | dq |
| 十字节BCD码(tenbyte) | tbyte | dt |
| 有符号字节(signbyte) | sbyte |  |
| 有符号字(signword) | sword |  |
| 有符号双字(signdword) | sdword |  |
| 单精度浮点数 | Real4 |  |
| 双精度浮点数 | Real8 |  |
| 10字节浮点数 | Real10 |  |

## 数据结构
```asm
结构名    struct

字段1     类型    ?
字段2     类型    ?
......

结构名    ends
```

**定义**
```asm
        .data?
变量名称    结构名    <字段1,字段2,...>
;或者
        .data?
变量名称    结构名    <>
```

**使用**
```asm
;前提假设结构名为WNDCLASS,结构体变量名为stWndClass,里面有字段lpfnWndProc

;1
mov    eax,stWndClass.lpfnWndProc

;2.esi寄存器作指针寻址
mov    esi,offset stWndClass
mov    eax,[esi + WNDCLASS.lpfnWndProc]    ;注意这里是WNDCLASS

;3.用assume伪指令把寄存器预先定义为结构指针
mov    esi,offset stWndClass
assume esi:ptr WNDCLASS
mov    eax,[esi].lpfnWndProc
...
assume esi:nothing    ;注意：不使用esi做指针的时候需要用这句取消定义

;4.结构的定义可以嵌套
NEW_WNDCLASS    struct

dwOption        word        ?
oldWndClass     WNDCLASS    <>

NEW_WNDCLASS    ends

;5.嵌套的引用
mov    wax,[esi].oldWndClass.lpfnWndProc
```

## 以不同的类型访问变量
```asm
;以db定义一个缓冲区
szBuffer    db    1024 dup (?)
;mov    ax,szBuffer ;错误，masm中，如果要用制定类型之外的长度访问变量，必须显式指出要访问的长度，这样编译器忽略语法上的长度检验，仅使用变量的地址
;类型 ptr 变量名
mov    ax,word ptr szBuffer
mov    eax,dword ptr szBuffer
```

## movzx
把一个字节扩展到一个字或一个字或一个双字再放到ax或eax中，高位保持0而不是越界存取到其他的变量
```asm
    .data
bTest1    db    12h
    .code
movzx    ax,bTest1
movzx    eax,bTest1
```

## 变量的尺寸和数量
```asm
sizeof    变量名、数据类型或数据结构名 ;取得变量、数据类型或数据结构以字节为单位的长度
lengthof  变量名、数据类型或数据结构名 ;取得变量中数据的项数
```

## 获取变量地址
```asm
mov    寄存器,offset 变量名    ;offset是取变量地址的伪操作符
lea    eax,[ebp-4]            ;运行时按照ebp的值实际计算出地址放到eax中
;invoke伪指令参数要用到一个局部变量的地址时，参数中不可能写入lea指令，用offset又是不对的，可用addr
addr   局部变量名和全局变量名   ;全局变量名时编译器按照odffset的用法来用；局部变量名时，编译器用lea先把地址取到wax中，然后用eax代替变量地址使用
;invoke中使用addr时，它的左边不能使用wax，否则eax的值会被覆盖
```

## 子程序的定义
```asm
子程序名 proc [距离][语言类型][可视区域][USES寄存器列表][,参数:类型]...[VARARG]
        local 局部变量列表

        指令

子程序名 endp
```

## 参数传递和堆栈平衡
**不同语言调用方式的差别**

| | C | SysCall | StdCall | BASIC | FORTRAN | PASCAL |
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
| 最先入栈参数 | 右 | 右 | 右 | 左 | 左 | 左 |
| 清除堆栈者 | 调用者 | 子程序 | 子程序 | 子程序 | 子程序 | 子程序 |
| 允许使用VARARG | 是 | 是 | 是 | 否 | 否 | 否 |

![](http://ww1.sinaimg.cn/large/c0264382gy1fjjg3l2dztj209w08f0ur.jpg)

## 条件测试语句
```asm
寄存器或变量    操作符    操作数
(表达式1) 逻辑运算符 (表达式2) 逻辑运算符 (表达式3) ...
;举例，左边表达式，右边是表达式为真的条件
x==3        ;x等于3
eax!=3      ;eax不等于3
(y>=3)&&ebx ;y大于等于3且ebx为非零值
;表达式的左边只能是变量或寄存器，不能为常数；表达式两边不能同时为变量，但可以同时是寄存器
```

标志位的状态指示
```
CARRY?        表示Carry位是否置位
OVERFLOW?     表示Overflow位是否置位
PARITY?       表示Parity位是否置位
SIGN?         表示Sign位是否置位
ZERO?         表示Zero位是否置位
```

## 分支语句
```asm
.if eax && (bx >= dWX) || !(dWY != ecx)
    mov    esi,1
.elseif edx
    mov    esi,2
.elseif esi & 1
    mov    esi,3
.elseif ZERO? && CARRY?
    mov    esi,4
.endif
```

## 循环语句
```asm
.while 条件测试表达式
    指令
    [.break [.if 退出条件]]
    [.continue]
.endw
;或
.repeat
    指令
    [.break [.if 退出条件]]
    [.continue]
    .until    条件测试表达式    (或 .untilcxz [条件测试表达式])
```

## 变量和函数的命名
### 匈牙利表示法
```
类型前缀+变量说明（类型用小写字母，说明则用首字母大写的几个引文单词组成）
```

汇编语言中常用的类型前缀

| | |
|:-----|:-----|
| b | 表示byte |
| w | 表示word |
| dw | 表示dword |
| h | 表示句柄 |
| lp | 表示指针 |
| sz | 表示以0结尾的字符串 |
| lpsz | 表示指向0结尾的字符串的指针 |
| f | 表示浮点数 |
| st | 表示一个数据结构 |

举例

| | |
|:-----|:-----|
| hWinMain | 主窗口的句柄 |
| dwTimeCount | 时间计数器，以双字定义 |
| szWelcome | 欢迎信息字符串，以0结尾 |
| lpBuffer | 指向缓存区的指针 |
| stWndClass | WNDCLASS结构 |

**本书的作者建议**
- 全局变量的定义使用标准的匈牙利表示法，在参数的前面加下划线；在局部变量的前面加@符号，这样引用的时候就能随时注意到变量的作用域。
- 在内部子程序的名称前面加下划线，以便和系统API区别。

举例
```asm
_Calc    proc    _dwX,_dwY
         local   @dwResult

         finit
         fild    _dwX
         fld     st(0)
         fmul                ;i * i
         fild    _dwY
         fld     st(0)
         fmul                ;j * j
         fadd                ;i * i + j * j
         fsqrt               ;sqrt(i * i + j * j)
         fistp   @dwResult   ;put result
         mov     eax,@dwResult
         ret

_calc    endp
```
