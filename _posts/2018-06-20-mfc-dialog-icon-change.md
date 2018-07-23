---
layout: post
title: "MFC 다이얼로그 아이콘 변경 및 추가"
autor: lynn.baek
date: 2018-06-20 13:26
tags: [MFC, Dialog]
comments: true
---

MFC 다이얼로그의 아이콘을 변경하고 추가하는 방법입니다.



### Source Code

```c++
BOOL TestApp::OnInitDialog()
{
     HICON hIcon = LoadIcon(AfxGetInstanceHandle(), MAKEINTRESOURCE(IDI_MAIN_ICON)); //icon 변경
     this->SetIcon(hIcon, FALSE); //icon 셋팅
}

```

