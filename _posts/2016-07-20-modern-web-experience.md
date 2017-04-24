---
layout: post
title: "modern web experience"
description: "지난 몇 주간 모던? 웹 경험 이야기"
tags: [web]
categories: [web]
---

# 들어가며
지난 몇 주간 [Spring Boot](http://projects.spring.io/spring-boot/), [SmartAdmin(AngularJS1 version)](https://github.com/antonioribeiro/bureau.io) 환경에서 [SVG](https://ko.wikipedia.org/wiki/%EC%8A%A4%EC%BC%80%EC%9D%BC%EB%9F%AC%EB%B8%94_%EB%B2%A1%ED%84%B0_%EA%B7%B8%EB%9E%98%ED%94%BD%EC%8A%A4) 기술을 이용한 화면 개발을 경험 해 보았다. 총 기간은 약 3주가 조금 안되는 기간(개인적으로 아주 긴 기간)이었고, 실제 개발을 한 기간은 대략 2주 정도 였다. 그동안 만들어 낸 결과물과 학습량은 아주 초라 하다.
허나, 앞으로 이런 경험은 할 수 없을 거 같고, 경험한 내용 정도는 기록해 둬야 할 거 같아, 몇 자 적는다.
개발툴은 eclipse 이고, 빌드 툴은 maven 이다.

# Web Back-end

## Spring Boot
[spring framework](https://spring.io/)를 사용 한다면, [spring boot](http://projects.spring.io/spring-boot/) 기반에서 application 개발을 진행 하는 것이 요즘 대세인가 보다. [spring boot](http://projects.spring.io/spring-boot/)의 모든 것을 살펴 볼 이유는 없고, 내가 사용했던 범위 만큼만 기술 한다.
[spring boot](http://projects.spring.io/spring-boot/)을 사용하여 web 개발을 하기 위해서는 pom.xml에 아래와 같이 기술 한다.

```
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.6.RELEASE</version>
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

[spring boot](http://projects.spring.io/spring-boot/)을 효과적으로 잘 이해하기 위해서는 본인이 사용하는 build tool의 개념과 원리를 잘 알아 볼 필요가 있다.
뭔 말이냐 하면, parent로 지정한 artifact에 기술된 pom.xml 내용을 적어도 한 번 쯤은 확인 해야 한 다는 말이다.
parent 쪽 pom.xml 파일을 확인 하였다면, 아래와 같이 properties를 왜 재 정의 했는지 이해할 수 있을 것이다.

```
	<properties>
		<java.version>1.8</java.version>
		<start-class>xxx.AppMain</start-class>
		<spring.version>4.3.1.RELEASE</spring.version>
		<m2eclipse.wtp.contextRoot>/</m2eclipse.wtp.contextRoot>
	</properties>
```

## spring-boot-starter-xxx
이 밖에 본인이 사용해야 할 기술 목록은 [여기서](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter-poms) 찾아, dependency에 추가 해 주면 된다.

## spring devtools
[spring-boot-devtools](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-devtools) 라는 artifact가 있다. 그리고 chrome 확장 프로그램에 [LiveReload](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-devtools-livereload)라는 툴도 있다. 이를 같이 사용하면 편안하게 개발 할 수 있다.

[동영상](https://youtu.be/A70NMxV13TI)을 참조 해 보자.

## spring boot 구동 원리
*호수를 유유히 떠다니는 백조가 우아해 보이는 이유는, 백조의 물갈퀴 발이 물 속에서 쉴새 없이 움직이고 있기 때문이다.*

[spring boot](http://projects.spring.io/spring-boot/)에 있는 sample을 돌려 보면, 내가 infra(db 관련 bean 같은 거...)성 bean을 spring에 등록 하지도 않았는데도, bean이 생성되어 DI를 받을 수 있고, 정말 나의 business logic만 구현 해도 될 것같은 착각이 든다. 그것도 빠른 시간내에 말이다.

maven dependencies 목록을 보면, spring-boot-autoconfigure-xxx.jar 파일이 있고, 내부에 META-INF/spring.factories 파일이 있다. 이 파일에는 XXXAutoConfiguration 클래스 이름이 적혀 있고, 이것들을 읽어 들여, @Conditional 로직에 맞추어 빈을 등록 하는 것이다.

## WebMvcAutoConfiguration
*web dev env구축은 선행 팀에서 이미 구축 해 놓은 상황 이었고, 나는 그 속에 들어가서 개발을 진행 하고 있었다.*

*나는 [jQuery](https://jquery.com/)만 사용할 줄 안다. 그것도 내가 필요한 만큼만. modern javascript 개발 패턴은 아는게 없다.*

하루는 개발을 하다가 html or js 파일을 수정하고, 전통적 방법으로 수정된 사항을 확인코자 chrome browser를 refresh 하였다. 반영이 안되었다. 몇 번을 refresh 해도 반영이 되지 않았다. chrome 설정에서 **인터넷기록삭제**를 한 후 다시 refresh 해도 안되었다. 그렇게 몇 분을 시도하고 고민하고 스트레스는 점점 쌓여만 갔다.

뭐가 문제였을까? 나는 eclipse로 개발 할 때, build automatically 옵션을 비활성화 한다. 이게 문제 였다.
web assets는 src/main/resources 내에 있었다.
어? 이게 여기 왜 들어가 있지? 어랏? 브라우져에 화면이 나오고 동작은 하네? 뭐지?

직접 내부 코드를 살펴 보게 된 동기는 이거였다.
WebMvcAutoConfigurationAdapter 코드를 살펴 보다 보면, addViewControllers 메소드가 있고, 이 것의 역할은 ParameterizableViewController를 등록하는 것이었다. 이때 [AngularJS 1](https://www.angularjs.org/) 개념도 나름 이해하게 되었다.

# Web Front-end

## Sublime Text
awesome

## NodeJS, npm
web front-end 개발을 위해서 반드시 설치해야 할 시스템인 것 같다.

## yeoman
영국 황실 근위병이라고... 그래서 파비콘 이미지가...
이건 사용하지 않았다. 검색을 통해 알게 되었다.

## bower
maven dependency 등록이라고 생각 하자.
이건 사용하지 않았다. SmartAdmin에서 이미 의존관계 설정이 되어 있다.
역으로, 이게 문제다. version up을 할 수 없다.

## gulp
maven life cycle 관리, plug-in 등록 및 action 설정 이라고 생각 하자.

## SmartAdmin
이거 만든 사람 고생 좀 했겠다.

## javascript
할 말이 없다. 어렵다. 그래도 신선했다. 일급객체가 function 이다.

## websocket, stomp

## Promise, $q
약속은 지킬수도 있고 안 지킬수도 있다. 이 상황은 모두 비동기 상황이다.
검색을 하면, 이미 많은 사람들이 정리해 두었다.

# Conclusion
웹 기반 기술이 이렇게 다양 한지, 알고는 있었지만, 다 남들 이야기 인 줄 알고 관심조차 없던 나에게 지난 시간은 감사하고, 소중 했다. 기회가 언제 다시 주어질 지는 모르겠지만, 기회가 주어진다면 javascript를 무자비 할 정도로 파 보고 싶다.