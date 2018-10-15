---
layout: post
title: " Ubuntu SVN 추천 :: RabbitVCS 설치하기"
author: lynn.baek
date: 2018-10-11 11:41
tags: [Ubuntu]
comments: true
---

기존에 Ubuntu 환경에서 svn으로 [rapidSVN](https://rapidsvn.org/)을 사용하고 있었습니다.

그런데 폴더나 파일에 직접 우클릭하여(tortoiseSVN 처럼) update나 commit등을 할 수 없기도 하고,

자꾸 svn이 꼬이는(?) 증상이 발생하여 svn을 다른 툴로 변경해보기로 했습니다.

그래서 찾아보던 중, RabbitVCS라는 svn이 tortoiseSVN와 비슷한 UI를 제공한다길래 사용해보았습니다.

아직 설치하고 checkout만 해본 상태인데, 확실히 rapidSVN보다는 편한 것 같습니다.

![rabbitvcs-nautilus-integration](/files/rabbitvcs-nautilus-integration.png)

## Ubuntu에서 RabbitVCS 설치

다운로드 페이지 : http://wiki.rabbitvcs.org/wiki/download



* 우선 update를 한 번 진행해줍니다.

```shell
sudo apt-get update
```

* RabbitVCS를 설치합니다.

```shell
sudo apt-get install rabbitvcs-nautilus
```

```shell
nautilus -q
```

```shell
nautilus &
```

적용이 됐는지 확인하고 재부팅합니다.
