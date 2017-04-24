---
layout: post
title: "spring batch complex big multine"
description: "complex big multine on spring batch"
tags: [spring-batch,multiline]
categories: [spring-batch]
---

# spring-batch
[spring-batch](http://projects.spring.io/spring-batch/)는 [spring](https://spring.io/)의 sub project로서 [ETL](https://en.wikipedia.org/wiki/Extract,_transform,_load)관련 수많은 API를 제공하고 있다.

장비에서 발생하는 데이터 파일([CSV](https://en.wikipedia.org/wiki/Comma-separated_values)포맷)을 파싱하여, DB 적재 프로그래밍을 수행하면서 [spring-batch](http://projects.spring.io/spring-batch/)를 이용하였다.

이를 수행 하면서, 겪었던 일을 정리한다.

# 주요 개발 항목
[spring-batch](http://projects.spring.io/spring-batch/)에서 제공하는 API 중 [FlatFileItemReader](http://docs.spring.io/spring-batch/trunk/reference/html/readersAndWriters.html#flatFileItemReader)를 이용해 [CSV](https://en.wikipedia.org/wiki/Comma-separated_values)파일을 읽어 드렸고, 데이터 적재는
[ItemWriter](http://docs.spring.io/spring-batch/trunk/reference/html/readersAndWriters.html#itemWriter)를 직접 구현하였다.

[spring-batch](http://projects.spring.io/spring-batch/)는 [Job](http://docs.spring.io/spring-batch/trunk/reference/html/domain.html#domainJob) 관리를 [JobRepository](http://docs.spring.io/spring-batch/trunk/reference/html/domain.html#domainJobRepository)를 통해서 하는데, 내 업무 환경에서는 단타성으로 많은 파일 처리를 해야 해서, [MapJobRepositoryFactoryBean](http://docs.spring.io/spring-batch/trunk/apidocs/org/springframework/batch/core/repository/support/MapJobRepositoryFactoryBean.html)을 이용하였다. 주의 점은
clear() 메소드를 호출해야 한다는 점이다. 않그러면, **OOME** 가 발생한다.

중요한 부분은 [ItemReader](http://docs.spring.io/spring-batch/trunk/reference/html/readersAndWriters.html#itemReader)를 구현하는 것이었는데,
이 부분은 [AggregateItemReader](https://github.com/spring-projects/spring-batch/blob/master/spring-batch-samples/src/main/java/org/springframework/batch/sample/domain/multiline/AggregateItemReader.java)를 참고하여 개발 하였다.

# CSV 파일 포맷 및 처리 전략
주요 정보는 제거하였다. 예제이므로 데이터 포맷 역시 간략화 하였다.
아래는 파일 데이터 이다.

```
HEADER_BEGIN
MACHINE_TYPE:HELLO
MACHINE_ID:HELLO001
FILE_CREATED_TIME:2016/11/11 11:11:11
HEADER_END

MATERIAL_DATA_BEGIN
MATERIAL_ID,RECIPE_ID,ITEM1,ITEM2,ITEM3
SPRING_BATCH_MULTILINE_000001,SPRING_BATCH,,,
MATERIAL_DATA_END

MACHINE_MATERIAL_DATA_BEGIN
MACHINE_ID,MATERIAL_ID,START_TIME,END_TIME,ITEM1,ITEM2,ITEM3
HELLO001,SPRING_BATCH_MULTILINE_000001,2016/11/11 11:00:00,2016/06/02 11:11:10,,,
MACHINE_MATERIAL_DATA_END

DEFECT_DATA_BEGIN
MATERIAL_ID,X,Y,JUDGE
SPRING_BATCH_MULTILINE_000001,-735.329,914.988,G
SPRING_BATCH_MULTILINE_000001,-735.329,914.988,G
SPRING_BATCH_MULTILINE_000001,-735.329,914.988,G
SPRING_BATCH_MULTILINE_000001,-735.329,914.988,G
...
DEFECT_DATA_END

```

데이터는 XXX_BEGIN, XXX_END내에 존재 하고 있다. 각 컨텐츠는 CRLF로 구분 되어 있다.
각 컨텐츠에는 컨텐츠에 대한 헤더 정보가 있고, 그 하단에 정보가 있다.
DEFECT_DATA 같은 경우는 수십만건이 있다.

특히, DEFECT data 경우는 상위에 있는, 자재, 장비 정보와 함께 Dataabase에 관리 되어야 한다.
이 처리를 위해 [ItemWriter](http://docs.spring.io/spring-batch/trunk/reference/html/readersAndWriters.html#itemWriter)에서, [StepExecution](http://docs.spring.io/spring-batch/trunk/reference/html/domain.html#domainStepExecution) 객체 내부 [ExecutionContext](http://docs.spring.io/spring-batch/trunk/reference/html/domain.html#domainExecutionContext) 에 자재, 장비 정보를 관리 하였다.

BEGIN, END로 감싸져 있는 정보 처리를 위해 Aggretation 하기 위한 Holder를 사용하였고, **OOME** 방지를 위해,
1000건씩 처리 하였다. Job 관련 spring-batch 설정 정보를 보면 commit interval이 1로 설정 되어 있다.
즉, BEGIN, END 쌍으로 처리를 하겠다는 것이고, 내부 데이터가 많을 경우를 대비하여, 1000건씩 처리를 하겠다는 의도 이다.

# 예제 환경
OS     : Linux
JVM    : JAVA 1.8
Spring : spring-boot (1.4.2)

[소스](https://github.com/jihwan/spring-batch-multiline-test)
