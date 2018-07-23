---
layout: post
title: "실행 중인 프로세스 리스트(Process List)가져오기"
author: lynn.baek
date: 2018-04-13 13:49
tags: [C++, ProcessList]
comments: true
---

실행 중인 Process List를 가져와서 Process ID와 Process Name을 stl map에 저장하여 리턴 해주는 함수입니다. 



### Source Code

```c++
#include <iostream>
#include <map>
#include <string>
#include <Windows.h>
#include <Tlhelp32.h>

using std::map;
using std::string;

map<DWORD, string> EnumProcs()
{
	map<DWORD, string> m;

	HANDLE snapshot = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
	if (snapshot != INVALID_HANDLE_VALUE)
	{
		PROCESSENTRY32 pe32 = { sizeof(PROCESSENTRY32) };
		if (Process32First(snapshot, &pe32))
		{
			do
			{
				m.insert(map<DWORD, string>::value_type(pe32.th32ProcessID, string(pe32.szExeFile)));
			} while (Process32Next(snapshot, &pe32));
		}
		CloseHandle(snapshot);
	}
	return m;
}

int main()
{
    int processId;
	string processName;
    
    map<DWORD, string> iMap = EnumProcs();
	map<DWORD, string>::iterator itMap;
    for (itMap = iMap.begin(); itMap != iMap.end(); itMap++)
    { 
        processId = itMap->first;
		processName = itMap->second;
        printf("processId : %d, processName : %ls\n", processId, processName.c_str());
    }
    
    return 0;
}
```

