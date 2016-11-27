---
title: influxDB history 에러 해결하기
date: 2016-09-05 21:08:33
categories:
- 개발
tags:
- influxDB
- IT
---
## 에러

Grafana에서 사용하려고 influxDB 를 Windows 7에서 빌드, 실행 했더니 influxDB shell에서 아래 에러가 발생하였다
``` sh
There was an error writing history file: open : The system cannot find the file specified.
```
<!-- more -->

influxDB는 로그를 HOME 환경변수로 설정된 경로에 저장하는데,
HOME 환경변수가 설정되지 않으면 에러가 발생한다고 한다.
{% blockquote github 답변 https://github.com/influxdata/influxdb/issues/6070 %}
The original issue this was opened for is actually fixed. The code no longer uses user.Current().
However it instead uses $HOME which now errors on windows, and on *nix when $HOME is unset.
{% endblockquote %}

## 해결

로그를 저장할 경로를 HOME 환경변수로 설정하면 된다
저장할 디렉토리 경로는 원하는 위치 아무데나-

### *nix 시스템

홈 디렉토리의 .bashrc나 .bash_profile 파일에 아래 명령어 추가
``` sh
export HOME=로그_저장할_디렉토리_경로
```

### Windows

HOME 시스템 변수 설정(Java 설치할때 JAVA_HOME 시스템 변수 등록하는 과정과 동일)