---
layout: post
title: "[스크랩] MFC 트레이 아이콘 잔상 없애기"
autor: lynn.baek
date: 2018-06-19 10:31
tags: [MFC, TrayIcon, 트레이아이콘]
comments: true
---

> 출처 :  [http://cpueblo.com/programming/api/contents/197.html](http://cpueblo.com/programming/api/contents/197.html )



### Source Code

```c++
struct TRAYDATA
{
	HWND hwnd;
	UINT uID;
	UINT uCallbackMessage;
	DWORD Reserved[2];
	HICON hIcon;
};

static HWND FindTrayToolbarWindow()
{
	HWND hWnd_ToolbarWindow32 = NULL;
	HWND hWnd_ShellTrayWnd;

	hWnd_ShellTrayWnd = ::FindWindow(_T("Shell_TrayWnd"), NULL);
	if (hWnd_ShellTrayWnd)
	{
		HWND hWnd_TrayNotifyWnd = ::FindWindowEx(hWnd_ShellTrayWnd, NULL, _T("TrayNotifyWnd"), NULL);

		if (hWnd_TrayNotifyWnd)
		{
			HWND hWnd_SysPager = ::FindWindowEx(hWnd_TrayNotifyWnd, NULL, _T("SysPager"), NULL);// WinXP

			// WinXP 에서는 SysPager 까지 추적            
			if (hWnd_SysPager)
				hWnd_ToolbarWindow32 = ::FindWindowEx(hWnd_SysPager, NULL, _T("ToolbarWindow32"), NULL);

			// Win2000 일 경우에는 SysPager 가 없이 TrayNotifyWnd -> ToolbarWindow32 로 넘어간다
			else
				hWnd_ToolbarWindow32 = ::FindWindowEx(hWnd_TrayNotifyWnd, NULL, _T("ToolbarWindow32"), NULL);
		}
	}
	return hWnd_ToolbarWindow32;
}

BOOL RefreshTrayIcon()
{
	HANDLE m_hProcess;
	LPVOID m_lpData;
	TBBUTTON tb;
	TRAYDATA tray;
	DWORD dwTrayPid;
	int TrayCount;

	// Tray 의 윈도우 핸들 얻기
	HWND m_hTrayWnd = FindTrayToolbarWindow();
	if (m_hTrayWnd == NULL)
		return FALSE;

	// Tray 의 개수를 구하고
	TrayCount = (int)::SendMessage(m_hTrayWnd, TB_BUTTONCOUNT, 0, 0);

	// Tray 윈도우 핸들의 PID 를 구한다
	GetWindowThreadProcessId(m_hTrayWnd, &dwTrayPid);

	// 해당 Tray 의 Process 를 열어서 메모리를 할당한다
	m_hProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, dwTrayPid);
	if (!m_hProcess)
		return FALSE;

	// 해당 프로세스 내에 메모리를 할당
	m_lpData = VirtualAllocEx(m_hProcess, NULL, sizeof(TBBUTTON), MEM_COMMIT, PAGE_READWRITE);
	if (!m_lpData)
		return FALSE;

	// Tray 만큼 루프
	for (int i = 0; i < TrayCount; i++)
	{
		::SendMessage(m_hTrayWnd, TB_GETBUTTON, i, (LPARAM)m_lpData);
		// TBBUTTON 의 구조체와 TRAYDATA 의 내용을 얻기
		ReadProcessMemory(m_hProcess, m_lpData, (LPVOID)&tb, sizeof(TBBUTTON), NULL);
		ReadProcessMemory(m_hProcess, (LPCVOID)tb.dwData, (LPVOID)&tray, sizeof(tray), NULL);

		// 각각 트레이의 프로세스 번호를 얻어서
		DWORD dwProcessId = 0;
		GetWindowThreadProcessId(tray.hwnd, &dwProcessId);

		// Process 가 없는 경우 TrayIcon 을 삭제한다
		if (dwProcessId == 0)
		{
			NOTIFYICONDATA        icon;
			icon.cbSize = sizeof(NOTIFYICONDATA);
			icon.hIcon = tray.hIcon;
			icon.hWnd = tray.hwnd;
			icon.uCallbackMessage = tray.uCallbackMessage;
			icon.uID = tray.uID;
			Shell_NotifyIcon(NIM_DELETE, &icon);
		}
	}

	// 가상 메모리 해제와 프로세스 핸들 닫기
	VirtualFreeEx(m_hProcess, m_lpData, NULL, MEM_RELEASE);
	CloseHandle(m_hProcess);

	return TRUE;
}
```

