---
layout: post
title: "MFC 프로그램 중복 실행 방지"
author: lynn.baek
date: 2018-06-20 11:49
tags: [MFC]
comments: true
---

MFC 프로그램의 중복 실행을 방지하는 방법입니다.

Dialog base 프로젝트도 [프로젝트명].cpp 파일의 InitInstance() 에 코드를 삽입해 줍니다.



### Source Code

```c++
BOOL CTestApp::InitInstance() 
{
	HANDLE hMutex = NULL;
	hMutex = CreateMutex( NULL,TRUE, _T("mtx_running_test_app"));
	if ( GetLastError() == ERROR_ALREADY_EXISTS )
	{
        AfxMessageBox(_T("프로그램이 이미 실행 중입니다."));
	}
}
```

