---
layout: post
title: "MFC Control 배경(background) color 투명하게 바꾸기"
author: lynn.baek
date: 2018-08-03 09:31
tags: [MFC]
comments: true
---



mfc 다이얼로그는 기본적으로 바탕색이 회색입니다.

그런데 UI를 꾸미다보면 흰색 테두리가 있는 이미지를 자연스럽게 다이얼로그에 입히고 싶거나 할 때

다이얼로그 색을 흰색 바탕으로 해줄 수 있습니다.



## 헤더파일(.h)

```c++
afx_msg HBRUSH OnCtlColor(CDC* pDC, CWnd* pWnd, UINT nCtlColor);
```



## 소스파일(.cpp)

```c++
BEGIN_MESSAGE_MAP(CLoadingDlg, CDialog)
	ON_WM_CTLCOLOR()
END_MESSAGE_MAP()
```

```C++
HBRUSH CLoadingDlg::OnCtlColor(CDC* pDC, CWnd* pWnd, UINT nCtlColor) 
{
	HBRUSH hbr = CDialog::OnCtlColor(pDC, pWnd, nCtlColor);

	UINT nID = pWnd->GetDlgCtrlID();

	switch (nID)
	{
	case IDC_STATIC_LOADING:
	case IDC_PIC_LOADING:
		pDC->SetBkMode(TRANSPARENT);
		hbr = (HBRUSH)GetStockObject(NULL_BRUSH);;
		break;
	}

	return hbr;
}
```


