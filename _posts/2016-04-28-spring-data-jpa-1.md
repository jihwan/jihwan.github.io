---
layout: post
title: "spring data jpa 1"
description: ""
tags: [spring-data-jpa]
categories: [spring-data-jpa]
---

# spring-boot with spring-data-jpa
spring boot로 모든 예제가 변경되어 많이 혼란 스럽다. <br>
spring-boot-autoconfigure.jar/META-INF/spring.factories 파일을 기반으로 동작 되는 것으로 생각된다. <br>
org.springframework.boot.autoconfigure.EnableAutoConfiguration 에 등록된 XXXAutoConfiguration 이 실행 된다. <br>
각 클래스는 @Conditional or @ConditionalXXX 가 실행 되어 각 조건별 bean 등록이 이루어 지는 것으로 일단 생각 된다. <br>
이 부분은 좀 더 학습을 진행 해 볼 필요가 있다. <br> 


# JpaRepositoriesAutoConfiguration
위 클래스는 spring-boot-autoconfigure.jar 에 있는 클래스다. <br>
이 클래스에는 @Import(JpaRepositoriesAutoConfigureRegistrar.class) 구문이 작성되어 있고 JpaRepositoriesAutoConfigureRegistrar 클래스가 시작점이다. <br>
즉, JpaRepositoriesAutoConfigureRegistrar 클래스가 spring-data-jpa 를 이용하여 작성한 bean을 spring bean으로 등록하는 가교 역할기능을 제공 하고 있다. <br>
실질적인 bean 등록은 spring-data-commons.jar에 있는 RepositoryConfigurationDelegate 클래스가 책임을 맡고 있다. <br>

> @EnableJpaRepositories 를 사용한다면, JpaRepositoriesRegistrar 를 사용하게 되는데, 이 부분 역시 확인해 볼 필요성이 보인다.


# RepositoryConfigurationDelegate
실제 bean 등록은 registerRepositoriesIn 메소드에서 실행 된다.
아래 코드가 핵심이다.

	public List<BeanComponentDefinition> registerRepositoriesIn(BeanDefinitionRegistry registry,
			RepositoryConfigurationExtension extension) {
		...

		for (RepositoryConfiguration<? extends RepositoryConfigurationSource> configuration : extension
				.getRepositoryConfigurations(configurationSource, resourceLoader, inMultiStoreMode)) {

			AbstractBeanDefinition beanDefinition = definitionBuilder.getBeanDefinition();
			String beanName = beanNameGenerator.generateBeanName(beanDefinition, registry);

			registry.registerBeanDefinition(beanName, beanDefinition);
			...
		}
		...
	}

즉, spring bean 으로 등록이 될 후보 bean들을 찾고 찾아낸 후보들에 대해 bean name과 bean definition 정보를
registry에 등록 함을 알 수 있다.

# JpaRepositoryConfigExtension
이 클래스는 spring-data-jpa 구현 기술을 이용한 spring candidate bean 찾기 기능을 담당하고 있다.

# TO DO
> 사실 spring-data-jpa의 DRY 코드 억제 방안 코드를 찾아보고자 했다. <br>
> 하지만 예상과 다르게 spring-boot로 부터 시작되는 코드와 annotaion, 빈 등록 전략의 코드를 보며, <br>
> 학습해야 할 내용이 존재 한다. <br>
> 아래 항목에 대한 학습 후, spring-data-jpa 좀 더 살펴 보도록 해야 겠다.

1. @Conditional, @ConditionalOnBean, @ConditionalOnClass, @ConditionalOnMissingBean, @ConditionalOnProperty, @ConditionalOnResource 
2. BeanDefinitionRegistry, ImportBeanDefinitionRegistrar
3. @AutoConfigureBefore, @AutoConfigureAfter, @AutoConfigureOrder