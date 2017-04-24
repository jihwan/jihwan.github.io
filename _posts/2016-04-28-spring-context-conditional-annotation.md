---
layout: post
title: "spring context Conditional annotation"
description: "spring-context @Conditional"
tags: [spring]
categories: [spring]
---

# @Conditional
spring-context 에 정의한 @Conditional은 bean 생성 조건을 이용(사용자가 Condition을 implementation 해야 함)하여, 빈 등록 제어가 가능하다. 주의할 점으로, @Conditional은 상속을 지원하지 않는 다는 것이다. <br>

> 이와 비슷한 역할을 하였던 것이 @Profile 이라고 생각 된다. <br>
> <b>현재 @Profile 역시 @Conditional을 사용한다. <br>

학습을 하다 보니 spring-boot-autoconfigure 에서 bean 로딩 전략이 이 @Conditional을 이용하는 방법이라고, 생각 된다.  
[@Conditional 테스트](https://github.com/jihwan/spring-study/blob/master/src/test/java/info/zhwan/context/MyConditionalTest.java){:target="_blank"}

# spring-boot 쪽에서 정의한 @ConditionalOnXXX
[@ConditionalOnBean](https://github.com/jihwan/spring-study/blob/master/src/test/java/info/zhwan/boot/autoconfigure/condition/ConditionalOnBeanTest.java "ConditionalOnBeanTest.java"){:target="_blank"}, 
[@ConditionalOnClass](https://github.com/jihwan/spring-study/blob/master/src/test/java/info/zhwan/boot/autoconfigure/condition/ConditionalOnClassTest.java "ConditionalOnClassTest.java"){:target="_blank"}, 
@ConditionalOnMissingBean 등등 종류도 다양 하다.
