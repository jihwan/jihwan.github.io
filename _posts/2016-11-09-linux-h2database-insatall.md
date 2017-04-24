---
layout: post
title: "linux h2database insatall"
description: "linux ubuntu에서 icon 생성"
tags: [linux, h2database]
categories: [linux]
---

# linux(ubuntu 14.04) h2database 설치

[h2database](http://www.h2database.com/html/main.html) 는 엄청나게 좋은 db이다.
Compatibility Mode(호환성 모드) 지원으로, Oracle, MsSql, MySql 등등으로도 사용 가능한 엄청난 db 이다.

요즘 linux mint를 사용하는 재미에 푹 빠져서, h2database 실행 icon을 만들고 싶었다.

이 방법은 [oracle sql developer 설치 방법](https://community.oracle.com/docs/DOC-888316)을 응용한 것이다.

# 방법
1. h2 jar 파일을 다운로드 받은 후 아래와 같이 이동

```
mkdir /opt/h2database
mv h2-1.4.193.jar /opt/h2database
```

2. h2database 실행 스크립트 작성

```
cd /usr/local/bin
vi h2database
```

3. h2database 실행 스크립트 내용

```
#!/bin/bash
java -cp "/opt/h2database/h2-1.4.193.jar:$H2DRIVERS:$CLASSPATH" org.h2.tools.Console "$@"
```

4. h2database 실행 가능하도록 변경

```
chmod 755 /usr/local/bin/h2database
```

5. desktop 파일 생성

```
cd /usr/share/applications
vi h2database.desktop
```

6. desktop 파일 내용

```
[Desktop Entry]
Name=h2database
Exec=/usr/local/bin/h2database
Terminal=false
StartupNotify=true
Icon=/opt/h2database/favicon.ico
Type=Application
```
