---
layout: post
title: "helloBower"
description: "bower 사용법"
tags: [bower]
categories: [dev-tool]
---

# 필요한 사항
> [노드를 설치합니다.](https://nodejs.org/ko/ "node.js")
> 아래 내용은 모두 cmd 창에서 실행 합니다. 저는 windows7 64bit 운영체제 입니다.

# 노드 설치 확인
	node -v
	npm -v

# bower 설치
	npm install -g bower

> -g 옵션은 global package로 설치 하겠다는 옵션입니다.
> 더 많은 내용은 npm help install

# 시작
	bower init

> 결과적으로 bower.json 파일이 생성 됩니다. cmd 상에서 몇가지 사항을 질문합니다.
> 모든 내용은 bower.json 파일에 기록 됩니다.

# bower json example
	{
	  "name": "jihwan.github.io",
	  "homepage": "http://zhwan.info",
	  "authors": [
	    "zhwan <zhwan.hwang@gmail.com>"
	  ],
	  "description": "zhwan's dev blog",
	  "main": "index.md",
	  "keywords": [
	    "dev",
	    "blog"
	  ],
	  "license": "MIT",
	  "ignore": [
	    "**/.*",
	    "node_modules",
	    "bower_components",
	    "test",
	    "tests"
	  ]
	}


# bootstrap 설치
	bower install bootstrap -S

# font-awesome 설치
	bower install font-awesome -S

# angularJs 설치
	bower install angularjs -S
	bower install angular-route -S
	bower install angular-ui-router -S

# 최종
> bower_components 폴더가 생성 되며, 하위에 설치한 library 는 각 폴더별로 생성 된다.
> 또, bower.json 파일 내부에 dependency 목록으로 등록이 된다.
> default로 bower_components 폴더 하위에 library가 설치 되는데, 폴더 명을 변경하고 싶다면,
> .bowerrc 파일을 만들고 아래와 같이 경로 명을 지정 할 수 있다.

	{
    		"directory": "assets/bower",
    		"scripts": {
        		"postinstall": "gulp publish"
    		}
	}

# 앞으로의 활용법
> bower가 설치 되어 있는 상황에서, bower.json 파일만 관리 한다면, <br>
> 모든 개발자가 같은 환경에서 dependency web library 관리를 쉽게 할 수 있다. <br>

	bower install // bower.json 파일에 등록된 dependency 를 설치 한다.

### npm?
> node package manager의 약자
