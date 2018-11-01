---
layout: post
title: "Ubuntu 한글 설치 방법"
author: lynn.baek
date: 2018-11-01 14:21
tags: [Ubuntu]
comments: true
---

우분투를 처음 설치했을 때, 한글은 기본으로 설치되어 있지 않아서, 한글을 사용하기 위해서는 별도의 설치 과정이 필요합니다.



## 우분투 한글 설치 방법

1. 한글 설치

   ```shell
   sudo apt-get install fcitx-hangul
   ```

2. `Language Support` > `Install/Remove Languages` > Korean 체크 > `Apply` 버튼 클릭

3. 설치 후 Language for menus and windows 항목에 `한국어`가 있는 지 확인

4. `한국어`가 보이지 않는다면 `Language Support` > `Install/Remove Languages` > Korean 체크 해제 > `Apply` 버튼 클릭 (Korean 제거)

5.  2번 항목 반복 (Korean 다시 설치)

6. `한국어`보이면 터미널 열어서 `ibus-setup` 커맨드 입력

7. IBus Preferences 창이 열린다.

8. `Input Method` 탭 > `Add` 버튼 클릭> `Korean`클릭 > `Hangul`클릭 > `Add` 버튼 클릭

9. `Settings` 열기 > `Region & Language` 탭 > Language를 `한국어`로 변경 > Input Sources에 `Korean(Hangul)` 추가

10. 재부팅

11. shift + space로 한/영 전환 가능해짐
