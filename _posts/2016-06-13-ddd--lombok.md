---
layout: post
title: "DDD 와 lombok"
description: "DDD 와 lombok"
tags: [DDD]
categories: [DDD]
---

# DDD 관점에서 Lombok
[lombok](https://projectlombok.org)을 처음 접했을 때가 2000년도 후반이었다. 거의 처음 나왔을때, 우연히 알게 되었다. 처음 접했을 때, 우와!!! 했던 기억이 있었다. [APT](http://docs.oracle.com/javase/1.5.0/docs/guide/apt/GettingStarted.html)기술을 이용한 기술이라고 알게 된 건 그 후로 몇 년 뒤였다.

최근 [DDD](https://en.wikipedia.org/wiki/Domain-driven_design)에 대해, 아니 [OOP](https://en.wikipedia.org/wiki/Object-oriented_programming)에 대한 공부를 다시 하면서, 많은 생각이 든다.
지금까지 왜? 내가 작성한 코드에 만족을 못했는지...
개인적으로, 가장 큰 이유는 이해력 부족과, 읽기 능력 혹은 어휘 능력 부족이었다.

이야기의 주제로 돌아와서, [lombok](https://projectlombok.org)은 잘 못 사용 할 경우 필요악이다.
<span style="text-decoration:line-through;">내가 작성하는 코드, 프로젝트에서 사라져야 할 dependency는 [lombok](https://projectlombok.org)이다. 만약 구지 사용해야 한다면, @Getter 정도만 사용해야 겠다.</span>

Type level에서 @Data @Setter 는 사용 하지 않는 편이 좋을 것 같다. @Getter는 field에 따로 지정 하는 것이 좋을 것 같다.