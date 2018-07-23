---
layout: post
title: "[스크랩]MFC에서 UI Thread를 이용한 Dialog 호출 방법"
date: 2018-06-15 15:49
author: lynn.baek
tags: [MFC, Dialog]
comments: true
---

[http://aladdin07.blog.me/150074142378](http://aladdin07.blog.me/150074142378)



### 1.목적

메인 프로그램에서 메뉴 항목을 선택하면 별도의 Dialog가 생성되어 거기서 정보를 입력 받는다.
단, 메인프로그램과 생성된 Dialog는 상호 독립적으로 구동되어야 한다.
(즉, 메인프로그램에서 DoModal()로 Dialog를 구동시키지 않고 별도 Thread로 구동 시켜야 한다)

### 2.방법

CWinThread Class를 활용한다.

1. ##### Class Wizard를 사용하여 CWinThread를 Base Class로하는 Class(CDlgThread)를 정의한다.

   ​    Header File(DlgThread.h)에는 `DECLARE_DYNCREATE(CDlgThread);`
       Source File(DlgThread.cpp)에는 `IMPLEMENT_DYNCREATE(CDlgThread ,CWinThread)`
       ClassWizard에 의해 자동으로 추가됩니다.

2. ##### DlgThread.h를 수정한다.

3. ##### protected로 정의된 Constructor DlgThread()와 Deconstructor ~DlgThread()를 public으로 바꾸어 준다. (이유는 해보면 안다)

   ```
     public:
          DlgThread();
          ~DlgThread();
   ```

4. ##### Dialog 변수를 정의 한다.

```
 public:
       CDialog* m_pDlg;
```

5. ##### CDlgThread의 InitInstance()에서 다음과 같이 독립실행 시킬 Dialog(CMyDlg)를 생성한다.

​    이 작업은 Thread에 Dialog를 Binding 시키는 과정이다.

```
 BOOL CDlgThread::InitInstance()
    {
        m_pDlg = new CMyDlg();
        m_pDlg->ShowWindow( SW_SHOW );
        m_pDlg->UpdateWindow();
        return TRUE;
    }
```

6. ##### CMyDlg Class의 Constructor를 수정한다.

```
CMyDlg::CMyDlg(CWnd* pParent /*=NULL*/)
         : CDialog(CMyDlg::IDD, pParent)
    {
        Create( IDD_MyDlg );    <== 요놈 추가
        //{{AFX_DATA_INIT(CDlgClub)
        // NOTE: the ClassWizard will add member initialization here
        //}}AFX_DATA_INIT
    }
```

7. ##### 필요한 곳에서 다음과 같이하여 Thread를 구동 시키면 된다.

    CDlgThread*  m_pThread = new CDlgThread();
    m_pThread->CreateThread();



※ 상기와 같이하면 UI Thread를 구현할 수 있다..간단하죠~~잉..?
    위와 같이 구동된 Thread Dialog는 OK, CANCEL 버튼을 눌러도 소멸되지 않습니다.
    단지 HIDE 상태로 되는 거죠..
    따라서, 명시적으로 Thread Dialog를 죽이지 않았다면..메인 프로그램이 끝날때까지 Thread로
    남아 있게되는 거죠....요놈을 다시 사용하고 싶을때는 다음 두 줄로 다시 나타나게 만들면 된다.

```
m_pThread->m_pDlg->ShowWindow( SW_SHOW );
    m_pThread->m_pDlg->UpdateWindow();
```

​    
※ 명시적으로 Thread Dialog를 종료시킬려면 간단하다.
    Thread Dialog에 WM_QUIT Message를 보내면 된다.
    어떻게..?  PostThreadMessage()를 사용하면 된다.

    m_pThread->PostThreadMessage(WM_QUIT,0,0);