---
layout: post
title: "C/C++ 배열의 요소 개수 구하기"
author: lynn.baek
date: 2018-08-14 14:59
tags: [C/C++]
comments: true
---



C/C++에서 배열의 요소 개수를 구하기 위해서 다음과 같은 매크로를 선언하여 사용할 수 있습니다.

```c++
#define _countof(_array)  sizeof(_array) / sizeof(_array[0])
```

