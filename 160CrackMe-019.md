title: 160CrackMe第十九Brad Soblesky.2
date: 2018-03-02 21:42:41
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



![](http://ww1.sinaimg.cn/large/c0264382gy1foyrcq7eydj207004jgli.jpg)
<!--more-->

查壳无壳，vc写的。  

我们输入假码后，然后点击，弹出错误框，直接打开od，对`MessageBoxA`下断点也行，寻找字符串也行。

一般的错误提示部分代码类似于这样。
```
    call xxx
    test xxx,xxx
    je xxxerror
    ...
    jmp xxx
    push xxx ;xxxerror
    ...

    call error
```
只需要往上找到关键跳直接nop就行。不过我们需要跟踪一下算法。

我们找到关键跳的`call`上方下断，可以看到他把一个东西压栈了，可以猜想是真码。

![](http://ww1.sinaimg.cn/large/c0264382gy1foyroy9x6yj20zm0g1ae2.jpg)

然后我们测试一下111111和1643803416，提示正确，那我们找到这段的段首下断，然后f9运行程序重新输入假码点击Check。重点观察1643803416的出现地。

![](http://ww1.sinaimg.cn/large/c0264382gy1foyrt7stnaj20wx0ecdj6.jpg)

我们可以看到在关键`call`的前方不远处就有出现，那么这个`add`前方的`call`是加密算法`call`吗？

显然不是的，我们可以看到这个`CString::Format`明显是对一个东西进行字符串格式化，格式是`%lu`(无符号长整数)，另外我们可以在它上面Enter跟一跟，可以发现直接从程序领空跳到系统领空了。所以我们可以猜测前面肯定是1643803416的一个什么数学形式然后用`%lu`格式化输出，我们可以推测是16进制，然后我们再重新来注意一下前面。

我们发现了1643803416的十六进制，在上方有个循环。其实之前在f8下来的时候，那个循环我们就可以推测是算法，现在经过分析可以更加肯定了。`mov eax,[local.4]`这个是这个循环最终跳出来的地方，那么`local.4`那里就是我们所需要找的东西。

![](http://ww1.sinaimg.cn/large/c0264382gy1foys1xvfuoj20rb0bwgnu.jpg)

在我们之前的两边跟中，我们可以测试发现`local.7`是你输入的Name的长度，`local.5`是我们输入的名字。

我们把上面的循环好好跟一遍。下面直接看我注释理解吧。对了，我们跟踪过程中也可以发现Name长度不能小于5，就在这个循环上方有个简单的判断。

```
004015BE  |> \C745 E0 00000>mov [local.8],0x0
004015C5  |.  EB 09         jmp short Brad_Sob.004015D0
004015C7  |>  8B55 E0       /mov edx,[local.8]
004015CA  |.  83C2 01       |add edx,0x1
004015CD  |.  8955 E0       |mov [local.8],edx                       ;  local8第一次进入循环为0，后续循环每次+1
004015D0  |>  8B45 E0        mov eax,[local.8]
004015D3  |.  3B45 E4       |cmp eax,[local.7]                       ;  local7 = len(name)
004015D6  |.  7D 42         |jge short Brad_Sob.0040161A             ;  当local8>=len(name)跳出循环
004015D8  |.  8B4D E0       |mov ecx,[local.8]
004015DB  |.  51            |push ecx
004015DC  |.  8D4D EC       |lea ecx,[local.5]                       ;  local5=name
004015DF  |.  E8 1C030000   |call Brad_Sob.00401900                  ;  取name[local8]的十六进制ascii放入al
004015E4  |.  0FBED0        |movsx edx,al
004015E7  |.  8B45 F0       |mov eax,[local.4]                       ;  local4初始值为0x81276345
004015EA  |.  03C2          |add eax,edx
004015EC  |.  8945 F0       |mov [local.4],eax                       ;  local4 += name[local8]的十六进制
004015EF  |.  8B4D E0       |mov ecx,[local.8]
004015F2  |.  C1E1 08       |shl ecx,0x8
004015F5  |.  8B55 F0       |mov edx,[local.4]
004015F8  |.  33D1          |xor edx,ecx
004015FA  |.  8955 F0       |mov [local.4],edx                       ;  local4 = (local8<<8)^local4
004015FD  |.  8B45 E0       |mov eax,[local.8]
00401600  |.  83C0 01       |add eax,0x1
00401603  |.  8B4D E4       |mov ecx,[local.7]
00401606  |.  0FAF4D E0     |imul ecx,[local.8]
0040160A  |.  F7D1          |not ecx
0040160C  |.  0FAFC1        |imul eax,ecx                            ;  eax = (~(len(name)*local8))*(local8+1)
0040160F  |.  8B55 F0       |mov edx,[local.4]
00401612  |.  0FAFD0        |imul edx,eax
00401615  |.  8955 F0       |mov [local.4],edx                       ;  local4 *= eax
00401618  |.^ EB AD         \jmp short Brad_Sob.004015C7
0040161A  |>  8B45 F0       mov eax,[local.4]

```

相信结合我的注释自己细看一遍应该不太费力。下面直接写注册算法。其实上面的基本上用伪代码都写的比较明白了。

```c
#include <stdio.h>
#include <string.h>

int main()
{
	// name为输入的第一个值 
	char* name = "111111";
	int len_name = strlen(name);
	
	if (len_name<5)
		// name小于5出现提示并退出 
		printf("User Name must have at least 5 characters.\n");
	else
	{
		long result = 0x81276345;
		for(int i = 0; i < len_name; i++)
		{
			result += name[i];
			result ^= (i<<8);
			result *= ~(len_name*i)*(i+1);
		}
		printf("result: %lu\n",result);
	}
	return 0;
}
```
