title: Win32汇编学习(5)：绘制文本2
date: 2018-02-08 15:55:46
update: ""
author: me
tags:
- ASM
- 读书笔记
- Windows
- 汇编(ASM)
categories:
- 汇编(ASM)
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



这次我们将学习有关文本的诸多属性如字体和颜色等。 
<!--more-->
## 理论：

Windows 的颜色系统是用RGB值来表示的，R 代表红色，G 代表绿色，B 代表蓝色。如果您想指定一种颜色就必须给该颜色赋相关的 RGB 值，RGB 的取值范围都是从 0 到 255，譬如您想要得到纯红色，就必须对RGB赋值（255，0，0），纯白色是 （255，255，255）。

您可以用函数 `SetTextColor` 和 `SetBkColor` 来“绘制”字符颜色和背景色，但是必须传递一个“设备环境”的句柄和 RGB 值作为参数。RGB 的结构体的定义如下： 

```
RGB_value struct
unused db 0
blue db ?
green db ?
red db ?
RGB_value ends
```

其中第一字节为 0 而且始终为 0，其它三个字节分别表示蓝色、绿色和红色，刚好和 RGB 的次序相反。这个结构体用起来挺别扭，所以我们重新定义一个宏用它来代替。该宏接收红绿蓝三个参数，并在 eax 寄存器中返回 32 位的 RGB 值，宏的定义如下：

```
RGB macro red，green，blue
xor eax，eax
mov ah，blue
shl eax，8
mov ah，green
mov al，red
endm 
```

您可以把该宏放到头文件中以方便使用。 

您可以调用 `CreateFont` 和 `CreateFontIndirect` 来创建自己的字体，这两个函数的差别是：前者要求您传递一系列的参数，而后者只要传递一个指向 `LOGFONT` 结构的指针。这样就使得后者使用起来更方便，尤其当您需要频繁创建字体时。在我们的例子中由于只要创建一种字体，故用 `CreateFont` 就足够了。在调用该函数后会返回所创建的字体的句柄，然后把该句柄选进“设备环境”使其成为当前字体，随后所有的“绘制”文本串的函数在被调用时都要把该句柄作为一个参数传递 

## 例子：

```
.386
.model flat, stdcall
option casemap:none

WinMain proto :DWORD,:DWORD,:DWORD,:DWORD

include windows.inc
include user32.inc
includelib user32.lib
include kernel32.inc
includelib kernel32.lib
include gdi32.inc
includelib gdi32.lib

RGB macro red,green,blue
	xor eax,eax
	mov ah,blue
	shl eax,8
	mov ah,green
	mov al,red
endm

.data
ClassName db "SimpleWinClass",0
AppName   db "Our Third Window",0
TestString db "Win32 汇编非常有意思",0
FontName db "script",0

.data?
hInstance HINSTANCE ?
CommandLine LPSTR ?

.code
start:
	invoke GetModuleHandle,NULL
	mov    hInstance,eax
	invoke GetCommandLine
	mov    CommandLine,eax
	invoke WinMain,hInstance,NULL,CommandLine,SW_SHOWDEFAULT
	invoke ExitProcess,eax

WinMain proc hInst:HINSTANCE,hPrevInst:HINSTANCE,CmdLine:LPSTR,CmdShow:DWORD 
    LOCAL wc:WNDCLASSEX 
    LOCAL msg:MSG 
    LOCAL hwnd:HWND 
    mov   wc.cbSize,SIZEOF WNDCLASSEX 
    mov   wc.style, CS_HREDRAW or CS_VREDRAW 
    mov   wc.lpfnWndProc, OFFSET WndProc 
    mov   wc.cbClsExtra,NULL 
    mov   wc.cbWndExtra,NULL 
    push  hInst 
    pop   wc.hInstance 
    mov   wc.hbrBackground,COLOR_WINDOW+1 
    mov   wc.lpszMenuName,NULL 
    mov   wc.lpszClassName,OFFSET ClassName 
    invoke LoadIcon,NULL,IDI_APPLICATION 
    mov   wc.hIcon,eax 
    mov   wc.hIconSm,eax 
    invoke LoadCursor,NULL,IDC_ARROW 
    mov   wc.hCursor,eax 
    invoke RegisterClassEx, addr wc 
    invoke CreateWindowEx,NULL,ADDR ClassName,ADDR AppName,\ 
           WS_OVERLAPPEDWINDOW,CW_USEDEFAULT,\ 
           CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,NULL,NULL,\ 
           hInst,NULL 
    mov   hwnd,eax 
    invoke ShowWindow, hwnd,SW_SHOWNORMAL 
    invoke UpdateWindow, hwnd 
    .WHILE TRUE 
        invoke GetMessage, ADDR msg,NULL,0,0 
        .BREAK .IF (!eax) 
        invoke TranslateMessage, ADDR msg 
        invoke DispatchMessage, ADDR msg 
    .ENDW 
    mov     eax,msg.wParam 
    ret 
WinMain endp

WndProc proc hWnd:HWND,uMsg:UINT,wParam:WPARAM,lParam:LPARAM
    
    LOCAL hdc:HDC
    LOCAL ps:PAINTSTRUCT
    LOCAL hfont:HFONT
    
    .IF uMsg==WM_DESTROY
        invoke PostQuitMessage,NULL
    .ELSEIF uMsg==WM_PAINT
        invoke BeginPaint,hWnd,ADDR ps
        mov    hdc,eax
        invoke CreateFont,24,16,0,0,400,0,0,0,OEM_CHARSET,\
                            OUT_DEFAULT_PRECIS,CLIP_DEFAULT_PRECIS,\
                            DEFAULT_QUALITY,DEFAULT_PITCH or FF_SCRIPT,\
                            ADDR FontName 
        invoke SelectObject,hdc,eax
        mov    hfont,eax
        RGB    200,200,50
        invoke SetTextColor,hdc,eax
        RGB    0,0,255
        invoke SetBkColor,hdc,eax
        invoke TextOut,hdc,0,0,ADDR TestString,SIZEOF TestString
        invoke SelectObject,hdc,hfont
        invoke EndPaint,hWnd,ADDR ps
    .ELSE
        invoke DefWindowProc,hWnd,uMsg,wParam,lParam
        ret
    .endif
    xor eax,eax
    ret
WndProc endp

end start
```

## 分析：

`CreateFont` 函数产生一种逻辑字体，它尽可能地接近参数中指定的各相关值。这个函数大概是所有 Windows API 函数中所带参数最多的一个。它返回一个指向逻辑字体的句柄供调用 `SelectObject` 函数使用。下面我们详细讲解该函数的参数：

```
CreateFont proto \
nHeight：DWORD，\
nWidth：DWORD，\
nEscapement：DWORD，\
nOrientation：DWORD，\
nWeight：DWORD，\ 
cItalic：DWORD，\ 
cUnderline：DWORD，\
cStrikeOut：DWORD，\
cCharSet：DWORD，\
cOutputPrecision：DWORD，\
cClipPrecision：DWORD，\
cQuality：DWORD，\
cPitchAndFamily：DWORD，\
lpFacename：DWORD
```

- `nHeight`： 希望使用的字体的高度，0为缺省。
- `nWidth`： 希望使用的字体的宽度，一般情况下最好用0， 这样 Windows 将会自动为您选择一个和高度匹配的值。因为在我们的例子中那样做的话会使得字符因太小而无法显示，所以我们设定它为16。
- `nEscapement`： 每一个字符相对前一个字符的旋转角度，一般设成0。900代表转90度，1800转190度，2700转270度。
- `nOrientation`： 字体的方向。
- `nWeight`： 字体笔画的粗细。

Windows 为我们预定义了如下值： 

```
FW_DONTCARE 等于 0
FW_THIN 等于 100
FW_EXTRALIGHT 等于 200
FW_ULTRALIGHT 等于 200
FW_LIGHT 等于 300
FW_NORMAL 等于 400
FW_REGULAR 等于 400
FW_MEDIUM 等于 500
FW_SEMIBOLD 等于 600
FW_DEMIBOLD 等于 600
FW_BOLD 等于 700
FW_EXTRABOLD 等于 800
FW_ULTRABOLD 等于 800
FW_HEAVY 等于 900
FW_BLACK 等于 900
```

- `cItalic`： 0为正常，其它值为斜体。 
- `cUnderline`： 0为正常，其它值为有下划线。
- `cStrikeOut`： 0为正常，其它值为删除线。
- `cCharSet`： 字体的字符集。一般选择OEM_CHARSET，它使得 Windows 会选用和操作系统相关的字符集。
- `cOutputPrecision`： 指定我们选择的字体接近真实字体的精度。 一般选用OUT_DEFAULT_PRECIS，它决定了缺省的映射方式。
- `cClipPrecision`： 指定我们选择的字体在超出裁剪区域时的裁剪精度。 一般选用CLIP_DEFAULT_PRECIS，它决定了裁剪精度。
- `cQuality`： 指定输出字体的质量。它指出GDI应如何尽可能的接近真实 字体，一共有三种方式：DEFAULT_QUALITY， PROOF_QUALITY 和DRAFT_QUALITY。
- `cPitchAndFamily`：字型和字体家族。
- `lpFacename`： 指定字体的名称。 

上面的描述不一定好理解，您如果要的到更多的信息，应参考 WIN32 API 指南。 

```
invoke SelectObject， hdc， eax
mov hfont，eax
```

在我们得到了指向逻辑字体的句柄后必须调用 `SelectObject` 函数把它选择进“设备环境”，我们还可以调用该函数把诸如此类的像颜色、笔、画刷 等GDI对象选进“设备环境”。该函数会返回一个旧的“设备环境”的句柄。您必须保存该句柄，以便在完成“绘制”工作后再把它选回。在调用 `SelectObject` 函数后一切的绘制函数都是针对该“设备环境”的。 

```
RGB 200，200，50
invoke SetTextColor，hdc，eax
RGB 0，0，255 
invoke SetBkColor，hdc，eax
```

我们用宏 RGB 产生颜色，然后分别调用 `SetTextColor` 和 `SetBkColor`。

```
invoke TextOut，hdc，0，0，ADDR TestString，SIZEOF TestString
```

我们调用 `TextOut` 在客户区用我们前面选定的字体和颜色“绘制”文本串。 `TextOut,hdc,x,y,lpString,nCount`

```
invoke SelectObject，hdc， hfont
```

在我们“绘制”完成后，必须恢复“设备环境”。

## 测试图
![绘制有颜色的文本](http://ww1.sinaimg.cn/large/c0264382gy1fo9dl4zzhsj20u00hk0sw.jpg)
