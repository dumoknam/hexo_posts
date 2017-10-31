---
title: 맥에서 한글 파이썬 모듈 import 하기
date: 2017-10-31 22:25:49
categories:
  - 개발
  - Django
tags:
  - Python
  - Python 3.x
  - Mac
---

### MAC! 한글! 파이썬 모듈!

unicode의 NFD, NFC 방식이 원인이다!
<!-- more -->
파이썬 모듈 중에 `models.회사` 가 있다고 하자.
~~한글 모듈을 사용하는 것이 옳은가는 논외로 한다~~
윈도우나 우분투에서 `from models.회사 import 회사` 로 import 하고 사용하는 것은 문제가 되지 않는다.

하지만 맥에서 위 구문을 그대로 사용하려고 하면, 해당 모듈을 찾을 수 없다는 `ModuleNotFoundError`가 발생한다.
맥의 유니코드는 NFD 방식인데, 여기서 한글을 표현하는 방식이 달라 생기는 문제이다.
(맥의 파일을 윈도우 유저에게 공유했더니 한글 파일명이 ㅇㅣㄹㅓㅋㅔ 자모음이 분리되는 현상을 겪은 사람이 있을 것이다. 같은 맥락이다. 윈도우는 NFC 방식을 사용한다. [관련링크](https://blogs.technet.microsoft.com/spsofficesupportko/2017/01/06/파일명의-한글자모가-분해되어-보여지는-현상-unicode-nfd/))

한글 모듈 사용을 위해서는 아래처럼 `unicodedata.normalize` 를 사용하면 된다.
{% gist aed7faccb87b4026c9c7725450f93365 mac_kr_import.py %}
