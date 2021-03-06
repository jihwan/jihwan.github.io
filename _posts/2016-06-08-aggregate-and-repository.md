---
layout: post
title: "Aggregate and Repository"
description: ""
tags: [DDD]
categories: [DDD]
---

###### 이 글은 [조영호님 블로그 DDD 편의 글](http://aeternum.egloos.com/category/Domain-Driven%20Design) 을 읽고, 정리한 글임을 밝힌다.

# Aggregate와 Repository
<img src="http://pds12.egloos.com/pds/200811/20/18/f0081118_4924fee94926b.jpg" ><br>
[출처](http://aeternum.egloos.com/1144679)

내용중에 아래와 같은 비즈니스 룰이 있다.

```
    주문 시 고객은 정해진 한도 내에서 상품을 구매할 수 있다.
    한도 초과 여부를 검증하는 책임을 어떤 도메인 객체에게 할당할까?
```
내가 관심갖는 부분은 Order와 Customer의 관계이다.
Order와 Customer는 다대일 관계이다. 반대로 이야기하면, Customer와 Order는 일대다 관계이다.
과거에 내가 ORM 책에서 읽었던 내용을 되짚어 보면, 양방향관계는 복잡해 진다는 언급되어 있다.
특히 양방향 연관관계에서 일대다쪽의 관계는 특별한 요구사항이 있을 경우에만 그 관계를 맺으라는 언급이 있다. 더 강력하게 표현한 다면, 이 경우 일대다 연관관계는 맺지 말아야 한다!

다시 돌아와서, 글의 내용에는 위에서 구현해야 할 비즈니스 룰의 책임은 Order에 있다고 친절하게 설명되어 있다.
협력하는 객체들 속에서 비즈니스 로직 구현 책임을 어떤 객체에 할당 해야 할까?
협력 객체내 관계를 곰곰히 생각한 후 그 책임이 어울리는 객체 쪽으로 할당해 주는 많은 연습이 필요 하겠다. 본문에서는 책임을 잘못 위치시킬 경우, 다른 객체의 내부 상태를 알고 있어야 하고, 이에 따라 결합도가 높아지는 폐해가 발생한다고.

이와 별개로, 주문은 Customer로 부터 시작된다. 왜? 도메인이 그러하니까...

글 내용에 등장하는 코드를 보면, 집요할 만큼 객체간 협력이 이루어지고 있다.
협력이 이뤄지고 있을때는 INFORMATION EXPERT 패턴을 준수하도록 하자.

# Order와 OrderLineItem
```
주문객체와 주문항목 객체들은 구매액이 고객의 주문 한도액을 초과할 수 없다는 불변식을 공유하는 하나의 논리적 단위
```
주문항목객체의 상태를 변경할 수 있는 책임은 주문밖에 없다. 다른 객체는 주문항목객체를 제어 해서는 안된다. 또한 주문객체는 주문항목객체를 노출해서는 안된다.
결론은, 주문객체는 주문항목객체를 캡슐화 해야 한다.

# Aggregate
- 협력하는 객체들의 논리적 묶음, 단위
- Aggregate에는 불변식이라 불리우는 어떤 규칙을 수행하는 행위(business logic)와 범위가 있다 (트랙잭션 처리 대상이 될 수 있다)
- Root와 Boundary 개념이 있다.
- Aggregate Root는 어떤 행위를 하기 위한 협력 객체 묶음 중 특정 Entity 객체 하나를 가르킨다.
- Aggregate Boundary는 경계 안의 객체는 Aggregate 내부 객체 끼리 서로 참조 가능하지만, 경계 밖의 객체는 Root만 참조 가능 하고, Aggregate 객체 내부는 참조 할 수 없다.
- 값만 가지고 있는 조금은 단순한 Entity도 Aggregate인가? 당연하다!!! 오해 하지 말자!!!

# Factory
객체를 생성하고자 할 때는 생성자를 이용하면 된다.
생성자를 통한 객체 생성을 통해, 그 객체의 기능을 사용한다면야 상관 없겠지만 보다 복잡한 객체를 생성해야 한 다면, Factory를 사용해야 겠다. 객체를 사용하는 쪽에서 복잡한 객체 생성에 대한 방식이 노출 되는 순간 이건 벌써 오염이다. 그리고, 이 순간 객체 생성이라는 책임의 개념이 나타났다는 것을 인지 하도록 노력해야 겠다. 생성되는 객체가 Type이라는 관점에서 다형성 개념과도 물려 있을 수 있다. 복잡할 수도 있겠구나.
Factory는 객체 생성시점과 database와 같은 저장소에 있는 정보를 가지고 재구성 할 때도 사용된다.
사용되는 시점에 따라 특징이 다르다. 재구성 될 때 가장 큰 차이점은 Entity생성을 하는 Factory의 경우, 이미 존재 하는 식별자를 Entity에 적용 시킨다. 이러한 기능은 아래 Repository가 담당하도록 하자!!!

Factory도 중요하겠지만 객체 생성 구현의 관점에서, 생성자 구현에도 좀 더 신경을 쓰도록 하자.

- 의미 없는 public 접근자를 사용하지 않는가?
- 의미 없는 default 생성자를 사용하지 않는가?
- 객체가 객체 내부 상태를 관리 한다면, 객체 생성시 반드시 초기화가 필요한 상태에 대해, 생성자에서 적절히 초기화 해 주는가? 그에 따른 규칙이 잘 이루어 지는가? NotNull 체크 같은...

# Repository
Repository는 application에 존재하는 Reference Object 혹은 Aggregate Object를 저장소에서 불러오거나, 저장할 책임을 지고 있다. 주의할 점은 Aggregate 내부에 존재하는 객체를 재구성하는 기능은 없어야 겠다.

# GRASP
General Responsibility Assignment Software Patterns
**OOD의 핵심 문제는 개별 객체에 역할 즉 책임을 부여하는 것이다.**

1. **Information Expert** : 역할을 수행할 수 있는 정보를 가지고 있는 객체에 부여하자. 객체 자신의 상태는 그 객체 스스로가 처리하도록 하여 자율성을 부여하자. 외부에는 그 기능, 역할을 제공한다. 쓸데 없는 getter, setter 메소드는 구현하지 말자. 좀 제발.


2. **Creator** : 객체의 생성은 생성되는 객체의 컨텍스트를 알고 있는 다른 객체가 있다면, 컨텍스트를 알고 있는 객체에 부여하자. A 객체와 B 객체의 관계의 관계가 다음 중 하나라면 A의 생성을 B의 역할로 부여하라.
- B 객체가 A 객체를 포함하고 있다.
- B 객체가 A 객체의 정보를 기록하고 있다.
- A 객체가 B 객체의 일부이다.
- B 객체가 A 객체를 긴밀하게 사용하고 있다.
- B 객체가 A 객체의 생성에 필요한 정보를 가지고 있다.


3. **Controller** : 시스템 이벤트(사용자 요청)를 처리할 객체를 만들자. 시스템, 서브시스템으로 들어오는 외부 요청을 처리하는 객체를 만들어 사용하자. 만약 어떤 서브시스템안에 있는 각 객체의 기능을 사용할 때, 직접적으로 각 객체에 접근하게 된다면 서브시스템과 외부간의 coupling이 증가되고, 서브시스템의 어떤 객체를 수정할 경우, 외부에 주는 충격이 크다. 서브시스템을 사용하는 입장에서 보면, 이 Controller 객체만 알고 있으면 된다.


4. **Low Coupling** : 객체들간, 서브시스템들간의 상호 의존도가 낮게 역할으르 부여하자. 객체세계에서는 객체간 관계를 맺어 협력을 한다. 따라서, 객체간 Coupling이 존재하지 않을 수 없다. 이 패턴은 요구사항은 충족시키면서도 각 객체들, 각 서브시스템 간의 Coupling을 낮은 수준으로 유지하는 방향으로 디자인하라고 한다. 역시, 양방향 연관관계는 필요악이로구나.


5. **High Cohesion** : 각 객체가 밀접하게 연관된 역할들만 가지도록 역할을 부여하자. Low Coupling과 비슷하면서 다른 개념이지만 땔수 없는 표현이다. 쓸데 없는 다른 객체를 참조 하지 말고, 필요한 만큼의 협력객체와 자기 자신에게 부여 받은 역할을 잘 수행하자.


6. **Polymorphism** : 객체의 분류, 타입에 행동 양식이 바뀐다면, Polymorphism 기능을 사용하자. 만약 객체의 분류, 타입에 따라 행동이 바뀐다면, 객체의 타입을 체크하는 조건문을 사용하지 말고 Polymorphism 을 사용하자.


7. **Pure Fabrication** :  Information Expert 패턴을 적용하면 Low Coupling과 High Cohesion의 원칙이 깨어진다면, 기능적인 역할을 별도로 한 곳으로 모으자. 데이터베이스 정보를 저장하거나, 로그 정보를 기록하는 역할에 대해 생각해 보자. 각 정보는 각각의 객체들이 가지고 있을 것이다. 이 때 Information Expert 패턴을 적용하면, 각 객체들이 정보를 저장하고, 로그를 기록하는 역할을 담당해야 하지만, 실제로 그렇게 사용하는 사람들은 없다. 이것은 그 기능들이 시스템 전반적으로 사용되고 있기 때문에 각 객체에 그 기능을 부여하는 것은 각 객체들이 특정 데이터베이스에 종속을 가져오거나, 로그을 기록하는 매커니즘을 수정할 경우, 모든 객체를 수정해야 하는 결과를 가져온다. 즉 Low Coupling의 원칙이 깨어지게 된다. 이럴 경우에는 공통적인 기능을 제공하는 역할을 한 곳으로 모아서 가상의 객체, 서브시스템을 만들어라.


8. **Indirection** : 두 객체 사이의 직접적인 Coupling을 피하고 싶으면, 그 사이에 다른 객체를 사용하라. 다른 객체는 대부분 interface인 경우가 많다. 그런 특별한 경우는 아래 Protected Variations 패턴이라한다.


9. **Protected Variations** : jdbc interface와 그 interface를 구현한 각 vendor의 상황



