---
layout: post
title: "Unicode환경에서의 CString to LPWSTR"
autor: lynn.baek
date: 2018-07-09 14:37
tags: [MFC, 형변환]
comments: true
---

Unicode환경에서 CString을 LPWSTR로 형변환 하는 방법입니다.



```c++
LPWSTR ConvertToUnicode(CString cString)
{
	int nBufSize = cString.GetLength() + 1;
	LPWSTR lpws = new wchar_t[nBufSize];

	if (lpws != NULL)
	{
		#if defined(_UNICODE)
		lstrcpy(lpws, cString); // If Unicode is defined, just copy the string.
		#else
		// mbstowcs() would work here as well...
		MultiByteToWideChar(CP_ACP, 0, cString, nBufSize, lpws, nBufSize * 2);
		#endif // _UNICODE
	}
	return lpws; // Caller must delete!
}
```

