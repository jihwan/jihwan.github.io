---
layout: post
title: "Hibernate custom Id Generator"
description: ""
tags: [hibernate]
categories: [hibernate]
---

# Custem Id Generator
최근 프로젝트에서 database table의 primary key 생성 전략을 TimeStamp(yyyyMMddHHmmss) + sequence 조합으로 사용하였다.

이를 jpa(hibernate 구현체)에서 사용하기 위해 어떤 방식으로 처리 해야 할 까?

우선 custom id generator를 구현하기 위해서는, jpa 구현체에서 제공하는 비표준 jpa 기술을 이용하여야 한다. hibernate에서는 IdentifierGenerator interface를 제공한다. (다른 jpa 구현체는 확인 하지 않았다.)

```java
public interface IdentifierGenerator {
	/**
	 * The configuration parameter holding the entity name
	 */
	public static final String ENTITY_NAME = "entity_name";

	/**
	 * The configuration parameter holding the JPA entity name
	 */
	public static final String JPA_ENTITY_NAME = "jpa_entity_name";

	/**
	 * Generate a new identifier.
	 *
	 * @param session The session from which the request originates
	 * @param object the entity or collection (idbag) for which the id is being generated
	 *
	 * @return a new identifier
	 *
	 * @throws HibernateException Indicates trouble generating the identifier
	 */
	public Serializable generate(SessionImplementor session, Object object) throws HibernateException;
}
```
위와 같이 generate를 overriding한다.
generate의 object argument는 entity 혹은 collection으로 명시 되어 있다.
만약, method에서 object argument를 활용한다면, 이에 대한 코드를 주의하여 작성 하도록 해야겠다.

마지막으로, entity에 custom id 생성 전략을 알려줘야 한다. 예제는 아래와 같다.

```java
	@Id
	@org.hibernate.annotations.GenericGenerator(name="orders_seq_id", strategy="ddd.support.OrderIdGenerator") // hibernate annotation
	@GeneratedValue(generator="orders_seq_id")
	@Column(length=2, precision=20)
	private BigDecimal id;
```

샘플코드

1. [Entity](https://github.com/jihwan/ddd-study/blob/master/ddd-example/src/main/java/ddd/Order.java)
2. [Custom Id Generator](https://github.com/jihwan/ddd-study/blob/master/ddd-example/src/main/java/ddd/support/OrderIdGenerator.java)

