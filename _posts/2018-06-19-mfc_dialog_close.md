---
layout: post
title: "[스크랩] MFC 프로그램 안전하게 종료하는 방법"
autor: lynn.baek
date: 2018-06-19 10:31
tags: [MFC, Dialog]
comments: true
---

> 출처 :  [https://blog.naver.com/ziralist/8325535](https://blog.naver.com/ziralist/8325535)



응용 프로그램을 종료한다는 의미는 해당 프로그램의 가장 기본이 되는 윈도우를 종료 시키는 것과 같다. 따라서 MFC로 작성한 응용 프로그램의 기본 골격이 되는 윈도우는 CFrameWnd(CWnd, CDialog) 계열의 클래스일 것이고 이 윈도우를 종료 시키면 응용 프로그램은 종료하게 된다. 



기본 골격이 되는 윈도우를 종료하는 가장 일반적인 방법은 해당 윈도우에 WM_CLOSE 메시지를 전달하면 된다. 이 메시지가 Main Frame 윈도우에 전달되면 처리기가 OnClose 함수를 호출하게 되고 OnClose함수는 저장하지 않은 작업을 저장하도록 요구하며 프로그램을 종료 시킨다. 아래의 코드는 WM_CLOSE 메시지를 전달하는 예제이다. 

`AfxGetMainWnd()->PostMessage(WM_CLOSE);`

또는

`AfxGetApp()->m_pMainWnd->PostMessage(WM_CLOSE);`

위 예제에서 SendMessage를 사용하지 않고 PostMessage 함수를 이용하여 메시지를 전달했음을 주의해야 한다. SendMessage 함수는 자신이 전달한 메시지가 처리될 때까지 기다리기 때문에 메시지 처리가 끝나고 나면 이미 윈도우가 파괴된 이후가 될 것이다. 따라서 SendMessage(WM_CLOSE); 코드를 사용한 이후에는 윈도우 관련 함수를 사용하면 안 된다. 이 점만 주의 한다면 SendMessage사용해도 상관없다. 물론 PostMessage를 사용하면 이런 점은 고려하지 않아도 된다. 



이 함수는 응용 프로그램의 프로세스(Main Thread)가 처리중인 메시지 루프를 종료 시켜 응용 프로그램을 종료 시킨다. 따라서 WM_CLOSE 메시지를 전달하는 것보다 좀더 직접적인 응용 프로그램 종료 방법이다. 그러나 이 방법을 사용하면 프로세스가 종료되기 때문에 응용 프로그램 종료 도중에 종료를 취소할 수 없다. PostQuitMessage함수는 종료 상태를 지정하는 한 개의 인자를 가지며 아래와 같이 사용하면 된다. 

`PostQuitMessage(0);`

응용 프로그램은 보통 한 개의 Thread로 구성되며 이 Thread가 응용프로그램 진행에 관련된 여러 가지 작업을 수행한다. 그 중에서도 가장 기본이 되는 것이 각종 메시지들에 대한 처리이다. 따라서 프로그램의 Main Thread는 메시지 처리 루프를 형성하게 되는데 이 메시지 루프의 종료 조건은 WM_QUIT이라는 메시지가 전달될 때까지 이다. 그리고 Main Thread는 메시지 루프를 종료한 직후, 응용 프로그램의 종료 루틴을 진행하게 된다. 결과적으로 PostQuitMessage는 프로그램의 Main Thread에게 WM_QUIT메시지를 전달하는 역할을 하는 함수임을 알 수 있다. 따라서 아래와 같이 사용해도 똑 같은 결과를 얻을 수 있다. 

`AfxGetMainWnd()->PostMessage(WM_QUIT); `



MFC는 프로그래머의 편의를 위하여 Application Wizard를 제공한다. 이 기능을 이용하여 MDI 또는 SDI 윈도우를 만들었을 때 만들어지는 여러 가지 소스와 자원 중에 메뉴 자원을 보면 Exit라는 항목이 존재하는데 우리가 특별한 코드를 작성하지 않아도 메뉴에서 이 항목을 선택하면 응용 프로그램이 종료한다. 즉, MFC가 기본적으로 이 메시지를 처리하고 있음을 알 수 있다. Exit 메뉴 항목의 속성을 보면 Command ID가 ID_APP_EXIT로 되어 있다. 그리고 메뉴 선택 시 발생하는 메시지는 WM_COMMAND이므로 아래와 같이 구성하여 Exit 항목을 선택한 것과 동일한 결과를 얻을 수 있다. 

`AfxGetMainWnd()->PostMessage(WM_COMMAND,ID_APP_EXIT);`

사실 위 코드를 C++의 Default Parameter 기법을 이용하여 마지막 인자가 생략되어 있다. 따라서 좀더 정확하게 코드를 고쳐 쓰면 아래와 같다. 

`AfxGetMainWnd()->PostMessage(WM_COMMAND,ID_APP_EXIT,0); `

여기에서 주의 해야 할 점은 마지막 인자를 이용하기 위해 0외에 다른 값을 사용해서는 안 된다는 것이다. 이 메시지는 메뉴에서 발생하도록 되어있던 것을 변칙적으로 사용하는 것이기 때문에 0외에 다른 숫자를 집어 넣으면 오류가 발생하게 된다. 