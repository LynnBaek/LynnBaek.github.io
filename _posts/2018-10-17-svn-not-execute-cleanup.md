---
layout: post
title: "Ubuntu SVN cleanup 안될 때"
author: lynn.baek
date: 2018-10-17 17:59
tags: [Ubuntu, SVN]
comments: true
---

Ubuntu에서 svn 사용 중에 간혹 cleanup이 안될 때가 있습니다. 

그럴 땐 다음과 같은 방법을 사용해보세요.



## Ubuntu Cleanup 에러 해결 방법

1. 문제가 생긴 svn 폴더(checkout한 root 경로)로 이동

2. 숨김폴더로 되어있는 ./svn 폴더로 이동

3. wc.db 파일있는지 확인

4. sqlite3 browser 설치(GUI 형태, CUI에서 나는 안되는 증상이 있었다.)

   ```shell
   sudo apt-get install sqlitebrowse
   ```

5. `데이터베이스 열기` 버튼 클릭하여 wc.db 파일 열기

   ![데이터베이스 열기 버튼 클릭](/files/Select Database.PNG)

6. `SQL 실행(Execute SQL)` 탭에서 `delete from work_queue` 이라는 SQL문 작성.

7. 위에 `▶` 버튼 클릭.

8. `변경사항 저장하기` 버튼 클릭

   ![SQL문 작성](/files/write SQL.PNG)

9. SVN Cleanup을 다시 시도 해본다. 

10. cleanup이 정상적으로 수행 됨.