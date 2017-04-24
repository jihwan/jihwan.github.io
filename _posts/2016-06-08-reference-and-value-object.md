---
layout: post
title: "Reference and Value object"
description: ""
tags: [DDD]
categories: [DDD]
---

###### 이 글은 [조영호님 블로그 DDD 편의 글](http://aeternum.egloos.com/category/Domain-Driven%20Design) 을 읽고, 정리한 글임을 밝힌다.

# Reference Object와 Value Object
객체 세계에서 Object는 Reference Object와 Value Object로 구성된다.

# Reference Object의 특징
- application 내에서 유일해야 한다. 즉 == 테스트를 통과되어야 한다.
- 추적이 가능해야 하므로, 식별성(identity)이 주어져야 한다. 식별성은 아주 중요 하다!
- 어떠한 행동에 의해 객체 내부 상태가 변한다. (불필요한 setter method를 만들지 말아라!!! 상태는 어떤 이벤트에 의한 행동의 결과로서 변화하는 것임을 잊지 말자!!!)
- 다른 Reference Object나 Value Object를 집합의 개념으로 포함시킬 수 있다. 포함하고 있는 객체들의 생명 주기를 관리 해야 한다. 객체망 탐색의 시작위치가 되고, 비즈니스 로직을 수행하는 시작점 역시 된다.

# Reference Object 설명 중 개인적인 생각
아래는 본문이다. ([Domain-Driven Design의 적용-1.VALUE OBJECT와 REFERENCE OBJECT 3부](http://aeternum.egloos.com/1114933))

	사용자 요청이 시스템내에 도착하면 시스템은 요청을 처리할 객체 그룹을 찾는다.
    이 객체 그룹중 entry point에 해당하는 reference object가 그룹을 대표하여 요청을 전달받고
    작업을 수행하기 위해 필요한 객체들과의 협력을 통해 요청을 완수한다.
    
사용자 요청은 무엇일까? Service객체라고 생각된다. application logic을 구현하거나 infra structure layer에서 특정 service를 제공하거나. 이 문맥에서는 application logic 즉, business flow를 정의한 것이 합당해 보인다.
application logic은 비즈니스 문제를 해결해야 하는 책임을 수행하기 위해, domain object, 동일 layer에 있는 application logic, 혹은 하위 layer에 있는 infra layer와도 협력해야 한다.
다시 돌아와서, 비즈니스 문제는 우리가 작성한 domain object가 해결해야 하는 책임이 있다.
그 중 후보가 되는 object는 entry point object이다. entry point object는 그 자체 내에서 위임등을 사용하여 비즈니스 문제를 해결한다.
또한 application layer에서 비즈니스 문제를 해결하기 위해, entry point object는 모두 초기화 되어야 한다. 이 초기화는 Repository가 책임을 진다. 나중에 학습할 jpa에서 lazy or eager fetch는 중요하지 않다. 객체가 초기화가 되어야 비즈니스 로직을 구현할 수 있으니 말이다.

기대효과는 무엇일까?
application logic은 자신의 책임에 알맞는 만큼의 역할을 수행한다. (두터운 로직을 작성할 필요가 없다.)
실제 도메인 로직은 도메인 객체가 수행하므로, 각자 오염되지 않은 깔끔한 상태를 유지 할 수 있다.
결국 이해하기 쉬운 코드가 작성되고, 변경에 탄력적으로 대응 할 수 있을 것으로 기대 된다.

# Value Object
- 각 속성들이 똘똘 뭉쳐져서 개념적으로 하나의 완전한 의미를 가져야 한다. ex) 사용자 정보내에 있는 주소 정보를 Address VO로 만드는 것
- 식별성이 필요 없다.
- 객체내 필드 값을 이용한 동등성 테스트를 한다. 따라서 equals와 hashcode 를 구현해야 한다.
- 불변 객체로 만들어라. (모든 속성은 생성자를 통해서 초기화가 된다. getter 메소드만 존재 하여 속성 수정 불가!)
- 자신의 속성에 변경이 가해지는 기능이 제공 된다면? 그 메소드에서는 결과를 새로이 반영한 새로운 VO 객체를 만들어서 제공해야 한다. 이는 불변 객체이어야 하기 때문이다.
- 보통 Reference Object의 생명주기에 종속적이다.

# Entity, VO on ORM (2016.06.25 추가)
소프트웨어로 구현해야 할 업무 도메인에서 모델을 식별 하고 그 결과, Entity와 VO들이 도출 되었다.
그리고, ORM기술을 이용 하기로 했다. 모든 것이 완벽하다.
하지만 위에서도 조금 언급 했지만, 객체는 관계를 맺고 있다. 아주 복잡하게.

ORM 기술을 사용한다고 해서, 특히 Entity 객체간 연관 관계를 어떤 기준(DDD에 나오는 Aggregate) 없이 맺으면 안 된다.
이건 아마 기술의 과함 내지 오용 이라고 생각한다.

다 떠나서, 객체는 관계를 맺는다. 관계(association)에는  composition, aggregation 관계가 있다.

## composition 관계는 한 객체가 다른 객체의 생명 주기를 완벽히 제어 한다.

```
생명 주기 제어 객체는 Entity이고, 종속 객체는 VO일 확률이 크다.
종속 객체는 행위적 측면 보다, 데이터 관리 측면이 더 높을 것이다.
제어 객체에서 종속 객체를 제어 할 것이다.

만약 종속객체가 VO라면, 이 VO는 그리 단순한 VO가 아닐 수도 있다.
무슨 말이냐 하면, 다른 Entity에서 몇가지 속성만 의도 적으로 도출 시킨 VO 형태 일 것이다.
```

## aggregation 관계는 각 객체의 생명 주기가 독립적이다. 그냥 관계 정도만 맺는다.

```
생명 주기가 독립적인데, 관계는 맺고 있다. 왜일까? 일단 데이터 관리는 해야 겠다.
여기서는, 관계 맺은 객체에 행위적 기능을 사용하고자 하는 의도가 있을 것이라고 생각한다.
즉, 객체 협력을 통해 내가 어떤 이득을 취하기 위함 일 것이다. 위임을 통해서...
```

관계에는 다중성, 방향성도 있다. 

방향성은 1차적으로 단방향 관계만 맺어 놓자. 나중에 필요 할 경우, 반때쪽 단방향 관계를 만들자.
서로 다른 방향 두개가 합쳐져 양방향이 되고, 이는 관계의 복잡성 증가와 관리 해야 할 기능 즉 코드가 많이 늘어난다. 조심히 다뤄야 겠다.

내가 지금 까지 이해한 수준에서, 정말 내가 잘 이해 하고 있는지, 정리 하기 위해 적어 본다.

역시 원리나 개념은 기술따위와 비교 할 수 없을 정도로 생명주기가 긴것 같다.

**단 원리나 개념은 내가 처한 상황에 따라 조금 다르게 해석 되거나 적용 시킬 수 있음을 명시 해야 한다.**


## 본글
[Domain-Driven Design의 적용-1.VALUE OBJECT와 REFERENCE OBJECT 1부](http://aeternum.egloos.com/1105776)

[Domain-Driven Design의 적용-1.VALUE OBJECT와 REFERENCE OBJECT 2부](http://aeternum.egloos.com/1111257)

[Domain-Driven Design의 적용-1.VALUE OBJECT와 REFERENCE OBJECT 3부](http://aeternum.egloos.com/1114933)

[Domain-Driven Design의 적용-1.VALUE OBJECT와 REFERENCE OBJECT 4부](http://aeternum.egloos.com/1115939)
