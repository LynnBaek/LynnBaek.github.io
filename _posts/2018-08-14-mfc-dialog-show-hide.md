---
layout: post
title: "MFC 메인 다이얼로그 실행하자마자 숨기기"
author: lynn.baek
date: 2018-08-14 14:31
tags: [MFC, Dialog]
comments: true
---



mfc 다이얼로그 기반 프로젝트에서 메인 다이얼로그를 실행하는 순간부터 숨기고 싶을 때가 있다.

OnInitDialog에서 SHOW_HIDE로 숨기면, 깜빡이면서 다이얼로그가 잠깐 나타났다가 숨겨지게 된다.

그래서 그 방법 보다는 WM_WINDOWPOSCHANGING 메시지를 추가하여 다이얼로그를 숨기는 방법이 좋다.



## 헤더파일(.h)

```
public:
	afx_msg void OnWindowPosChanging(WINDOWPOS* lpwndpos);
```



## 소스파일(.cpp)

```c++
BEGIN_MESSAGE_MAP(CMyDialog, CDialog)
	ON_WM_WINDOWPOSCHANGING()
END_MESSAGE_MAP()
```

```c++
void CMyDialog::OnWindowPosChanging(WINDOWPOS* lpwndpos)
{
	lpwndpos->flags &= ~SWP_SHOWWINDOW;

	CDialog::OnWindowPosChanging(lpwndpos);
}
```
