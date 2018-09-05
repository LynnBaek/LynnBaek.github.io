---
layout: post
title: "MFC 다이얼로그 선언되지 않은 식별자입니다"
author: lynn.baek
date: 2018-08-14 14:31
tags: [MFC, Error]
comments: true
---



mfc 다이얼로그 리소스를 추가하고, 클래스 파일을 생성했을 때, 다음과 같은 에러를 만나는 경우가 있다.

![mfc_error_dialog_not_found](/files/mfc_error_dialog_not_found.PNG)

```
식별자 "IDD_XXXXX"이(가) 정의되어 있지 않습니다.
'IDD_XXXXX' : 선언되지 않은 식별자입니다.
```



이런 경우, 다이얼로그가 정의되어있는 resource 헤더를 추가해주면 해결된다.

소스에 다음 헤더를 추가해주자.

```c++
#include "resource.h"
```

