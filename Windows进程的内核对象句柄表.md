title: Windows进程的内核对象句柄表
date: 2018-02-04 17:22:00
update: ""
author: me
tags:
- 二进制
- 系统
- Windows
categories:
- Windows
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



当一个进程被初始化时,系统要为它分配一个句柄表。该句柄表只用于内核对象 ,不用于用户对象或GDI对象。  

![](http://ww1.sinaimg.cn/large/c0264382gy1foaobsqowpj20nz05c0t3.jpg)

<!--more-->

## 创建内核对象
当进程初次被初始化时，它的句柄表是空的。然后，当进程中的线程调用创建内核对象的函数时，比如CreateFileMapping，内核就为该对象分配一个内存块，并对它初始化。这时，内核对进程的句柄表进行扫描，找出一个空项。由于表 3 - 1中的句柄表是空的，内核便找到索引1位置上的结构并对它进行初始化。该指针成员将被设置为内核对象的数据结构的内存地址，访问屏蔽设置为全部访问权，同时，各个标志也作了设置。  

下面列出了用于创建内核对象的一些函数（不是个完整的列表）：

```cpp
HANDLE CreateThread(
    PSECURITY_ATTRIBUTE psa,
    DWORD dwStackSize,
    LPTHREAD_START_ROUTINE pfnStartAddr,
    PVOID pvParam,
    DWORD dwCreationFlags,
    PDWORD pdwThreadId);

HANDLE CreateFile(
    PCTSTR pszFileNAme,
    DWORD dwDesiredAccess,
    DWORD dwShareMode,
    PSECURITY_ATTRIBUTES psa,
    DWORD dwCreationDistribution,
    DWORD dwFlagsAndAttributes,
    HANDLE hTemplateFile);

HANDLE CreateFileMapping(
    HANDLE hFile,
    PSECURITY_ATTRIBUTES psa,
    DWORD flPRotect,
    DWORD dwMaximumSizeHigh,
    DWORD dwMaximumSizeLow,
    PCTSTR pszName);

HANDLE CreateSemaphore(
    PSECURITY_ATTRIBUTES psa,
    LONG lInitialCount,
    LONG lMaximumCount,
    PCTSTR pszName);
```
用于创建内核对象的所有函数均返回与进程相关的句柄，这些句柄可以被在相同进程中运行的任何或所有线程成功地加以使用。该句柄值实际上是放入进程的句柄表中的索引，它用于标识内核对象的信息存放的位置。 因此当调试一个应用程序且观察内核对象句柄的实际值时，会看到一些较小的值，如1，2等。  

每当调用一个将内核对象句柄接受为参数的函数时，就要传递由一个 Create*&函数返回的值。从内部来说，该函数要查看进程的句柄表，以获取要生成的内核对象的地址，然后按定义得很好的方式来生成该对象的数据结构。  

如果传递了一个无效索引（句柄），该函数便返回失败，而GetLastError则返回 6（ERROR_INVALID_HANDLE）。由于句柄值实际上是放入进程句柄表的索引，因此这些句柄是与进程相关的，并且不能由其他进程成功地使用。  

如果调用一个函数以便创建内核对象，但是调用失败了，那么返回的句柄值通常是0（NULL）。发生这种情况是因为系统的内存非常短缺，或者遇到了安全方面的问题。不过有少数函数在运行失败时返回的句柄值是-1（INVALID_HANDLE_VALUE）。例如，如果CreateFile未能打开指定的文件，那么它将返回INVALID_HANDLE_VALUE，而不是返回NULL。当查看创建内核对象的函数返回值时，必须格外小心。特别要注意的是，只有当调用CreateFile函数时，才能将该值与INVALID_HANDLE_VALUE进行比较。下面的代码是不正确的：

```cpp
HANDLE hMutex = CreateMutex(...);
if (hMutex == INVALID_HANDLE_VALUE) {
    //这段代码不会执行，因为CreateMutex调用失败的时候返回的是NULL
}
```
同样的，下面的代码也不正确：
```cpp
HANDLE hFile = CreateFile(...);
if (hFIle == NULL) {
    //这段代码不会执行，因为CreateFile调用失败的时候返回的是INVALID_HANDLE_VALUE(-1)
}
```

## 关闭内核对象
无论怎样创建内核对象，都要向系统指明将通过调用C l o s e H a n d l e来结束对该对象的操作：

```cpp
BOOL CloseHandle(HANDLE hobj);
```

如果该句柄是有效的，那么系统就可以获得内核对象的数据结构的地址，并可确定该结构中的使用计数的数据成员。如果使用计数是0，该内核便从内存中撤消该内核对象。  

如果将一个无效句柄传递给CloseHandle，将会出现两种情况之一。如果进程运行正常，CloseHandle返回FALSE，而GetLastError则返回ERROR_INVALID_HANDLE。如果进程正在排除错误，系统将通知调试程序，以便能排除它的错误。 

在CloseHandle返回之前，它会清除进程的句柄表中的项目，该句柄现在对你的进程已经无效，不应该试图使用它。无论内核对象是否已经撤消，都会发生清除操作。当调用CloseHandle函数之后，将不再拥有对内核对象的访问权，不过，如果该对象的使用计数没有递减为0，那么该对象尚未被撤消。这没有问题，它只是意味着一个或多个其他进程正在使用该对象。当其他进程停止使用该对象时（通过调用CloseHandle），该对象将被撤消。  

假如忘记调用CloseHandle函数，那么会不会出现内存泄漏呢？答案是可能的，但是也不一定。在进程运行时，进程有可能泄漏资源（如内核对象）。但是，当进程终止运行时，操作系统能够确保该进程使用的任何资源或全部资源均被释放，这是有保证的。对于内核对象来说，系统将执行下列操作：当进程终止运行时，系统会自动扫描进程的句柄表。如果该表拥有任何无效项目（即在终止进程运行前没有关闭的对象），系统将关闭这些对象句柄。如果这些对象中的任何对象的使用计数降为0，那么内核便撤消该对象。  

因此，应用程序在运行时有可能泄漏内核对象，但是当进程终止运行时，系统将能确保所有内容均被正确地清除。另外，这个情况适用于所有对象、资源和内存块，也就是说，当进程终止运行时，系统将保证进程不会留下任何对象。  

## 参考文献：
- 《Windows核心编程》
