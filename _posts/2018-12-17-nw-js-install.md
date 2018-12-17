# NW.js(node-webkit) 설치 방법

Last Edited: Dec 17, 2018 9:44 AM
Tags: cross-platform,node-webkit,nw.js

# 설치하기

---

1. [https://github.com/rogerwang/node-webkit#downloads](https://github.com/rogerwang/node-webkit#downloads)
2. 다운받은 파일을 압축 해제한 후, 폴더 내의 `nw.exe`를 실행한다.
3. 다음과 같이 크로미움 기반의 웹뷰 화면이 나오게 됩니다. 

    ![](/files/Untitled-f2278f80-5419-41fe-a287-23205c5074ea.png)

# Hello World 띄워보기

---

1. 임의의 폴더를 하나 생성한다. ex) app
2. index.html 만들기

        <!DOCTYPE html>
        <html>
          <head>
            <title>Hello World!</title>
          </head>
          <body>
            <h1>Hello World!</h1>
            We are using node.js <script>document.write(process.version)</script>.
          </body>
        </html>

3. package..json 만들기

        {
          "name": "nw-demo",
          "version": "0.0.1",
          "main": "index.html"
        }

4. 아까 생성한 폴더(app)에 index.html과 package.json을 넣는다.
5. 폴더를 통째로 nw.exe로 드래그

    ![](/files/Untitled-8751c279-c1d0-4e72-82f8-9cc2e7d2db17.png)

6. 다음과 같이 nw.js가 열리면서 Hello Wolrd!를 출력하게 된다.

    ![](/files/Untitled-f7d51f00-c1b6-43b3-be99-7cd23bc2fb56.png)

# 독립된 설치 패키지 만들기

---

1. index.html과 package.json 파일이 들어있는 폴더(app)를 압축시킨다.

    ![](/files/Untitled-84573717-89ca-4e7d-84f0-14accb5c3dcb.png)

2. 확장자 `.zip`을 `.nw`로 변경한다.

    ![](/files/Untitled-b3828d03-2b36-4310-89ff-a10f18aff5a5.png)

3. 명령프롬프트(`cmd`) 창에서 nw.exe가 있는 폴더로 이동한다.

    ![](/files/Untitled-64bccbbe-589b-4722-b64d-79ab920213f9.png)

4. 다음 명령어를 실행한다.

`copy /b nw.exe+app.nw app.exe`

5. `app.exe`라는 실행파일이 생성된다. 

    ![](/files/Untitled-1a258f4f-d0e1-4ddf-9909-493f1c021d1c.png)

6. `app.exe` 를 실행시키면 다음과 같이 stand-alone 형태로 실행되게 된다. 

    ![](/files/Untitled-ad174626-7f80-4067-8ccd-04fdbbe95d0b.png)