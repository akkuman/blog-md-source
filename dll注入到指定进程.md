title: dll注入到指定进程
date: 2017-10-24 13:21:30
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



talk is cheap,show me code
代码有详细注释，文章底部提示了一些坑
<!--more-->

## 主程序

```cpp
#include "stdafx.h"
#include <windows.h>
#include <iostream>
#include <tlhelp32.h>
#include <tchar.h>


using namespace std;

int EnableDebugPriv(char* name)
{
	HANDLE hToken;
	TOKEN_PRIVILEGES tp;
	LUID luid;
	//打开进程令牌环
	OpenProcessToken(GetCurrentProcess(), TOKEN_ADJUST_PRIVILEGES | TOKEN_QUERY, &hToken);
	//获得进程本地唯一ID
	LookupPrivilegeValue(NULL, name, &luid);

	tp.PrivilegeCount = 1;
	tp.Privileges[0].Attributes = SE_PRIVILEGE_ENABLED;
	tp.Privileges[0].Luid = luid;
	//调整权限
	AdjustTokenPrivileges(hToken, 0, &tp, sizeof(TOKEN_PRIVILEGES), NULL, NULL);
	return 0;
}

//*****************************************************************************************************************************

BOOL InjectDll(LPCSTR DllFullPath, const DWORD dwRemoteProcessId)
{
	// 提升权限(必须管理员身份)
	EnableDebugPriv(SE_DEBUG_NAME);

	//打开远程线程
	HANDLE hRemoteProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, dwRemoteProcessId);
	if (hRemoteProcess == NULL)
	{
		cout << "Error: OpenProcess failed!\n" << endl;
		return FALSE;
	}

	//使用VirtualAllocEx函数在远程进程的内存地址空间分配DLL文件名空间
	LPVOID pszLibFileRemote = VirtualAllocEx(hRemoteProcess, NULL, lstrlen(DllFullPath) + 1, MEM_COMMIT, PAGE_READWRITE);
	if (pszLibFileRemote == NULL)
	{
		CloseHandle(hRemoteProcess);
		cout << "Error: VirtualAllocEx failed!\n" << endl;
		return FALSE;
	}

	//使用WriteProcessMemory函数将DLL的路径名写入到远程进程的内存空间
	if (!WriteProcessMemory(hRemoteProcess, pszLibFileRemote, DllFullPath, lstrlen(DllFullPath) + 1, NULL))
	{
		CloseHandle(hRemoteProcess);
		cout << "Error: WriteProcessMemory failed!\n" << endl;
		return FALSE;
	}

	//启动远程线程LoadLibraryA，通过远程线程调用创建新的线程
	HANDLE hRemoteThread;
	if ((hRemoteThread = CreateRemoteThread(hRemoteProcess, NULL, 0, (LPTHREAD_START_ROUTINE)LoadLibraryA, pszLibFileRemote, 0, NULL)) == NULL)
	{
		CloseHandle(hRemoteProcess);
		cout << "Error: the remote thread could not be created.\n" << endl;
		return FALSE;
	}
	else
	{
		// 等待线程退出 要设置超时 以免远程线程挂起导致程序无响应
		//WaitForSingleObject(hRemoteThread, 10000);
		// 如果等待线程 DLL中的DllMain不要写MessageBox
		cout << "Success: the remote thread was successfully created.\n" << endl;
	}

	// 释放句柄
	CloseHandle(hRemoteProcess);
	CloseHandle(hRemoteThread);

	return TRUE;
}

// 根据进程名称获取进程ID
DWORD FindTarget(LPCSTR lpszProcess)
{
	DWORD dwRet = 0;
	HANDLE hSnapshot = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
	PROCESSENTRY32 pe32;
	pe32.dwSize = sizeof(PROCESSENTRY32 );
	Process32First(hSnapshot, &pe32 );
	do
	{
		if (lstrcmpi(pe32.szExeFile, lpszProcess) == 0)
		{
			dwRet = pe32.th32ProcessID;
			break;
		} 
	} while (Process32Next(hSnapshot, &pe32));
	CloseHandle(hSnapshot);
	return dwRet; 
}


//*****************************************************************************************************************************

int main()
{

	DWORD id = FindTarget((LPCSTR)"calc.exe");
	cout << id << endl;

	// 获取可执行文件所在目录
	TCHAR szFilePath[MAX_PATH + 1];
	GetModuleFileName(NULL, szFilePath, MAX_PATH);
	*(_tcsrchr(szFilePath, '\\')) = 0;

	_tcscat_s(szFilePath, sizeof(szFilePath), "\\dll.dll");
	cout << szFilePath << endl;
	InjectDll(szFilePath, id);//这个数字是你想注入的进程的ID号
	return 0;
}
```

## dllmain
```cpp
// dllmain.cpp : 定义 DLL 应用程序的入口点。
#include "stdafx.h"

#include <iostream>

using namespace std;

BOOL APIENTRY DllMain(HINSTANCE hInst     /* Library instance handle. */,
	DWORD reason        /* Reason this function is being called. */,
	LPVOID reserved     /* Not used. */)
{
	switch (reason)
	{
	case DLL_PROCESS_ATTACH: //当这个DLL被映射到了进程的地址空间时
		MessageBox(0, TEXT("From DLL\n"), TEXT("Process Attach"), MB_ICONINFORMATION);
		cout << "Process Attach" << endl;
		break;

	case DLL_PROCESS_DETACH: //这个DLL从进程的地址空间中解除映射
		MessageBox(0, TEXT("From DLL\n"), TEXT("Process Detach"), MB_ICONINFORMATION);
		cout << "Process Detach" << endl;
		break;

	case DLL_THREAD_ATTACH: //一个线程正在被创建
		MessageBox(0, TEXT("From DLL\n"), TEXT("Thread Attach"), MB_ICONINFORMATION);
		cout << "Thread Attach" << endl;
		break;

	case DLL_THREAD_DETACH: //线程终结
		MessageBox(0, TEXT("From DLL\n"), TEXT("Thread Detach"), MB_ICONINFORMATION);
		cout << "Thread Detach" << endl;
		break;
	}

	return TRUE;
}
```

## 需要注意的地方

1. 环境是vs，字符集是多字节
2. 这份代码中的`hRemoteThread = CreateRemoteThread(hRemoteProcess, NULL, 0, (LPTHREAD_START_ROUTINE)LoadLibraryA, pszLibFileRemote, 0, NULL)`中的也可采用GetProcAddress函数  
3. 这份代码并不是通用注入代码（如果需要通用需要自行解析pe头结构从中取出kernel32.dll的GetProcAddress地址）,所以64位windows上需要把vs设置为编译x64
