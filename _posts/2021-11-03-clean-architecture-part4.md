---
title: '[기술서적 리뷰] Clean Architecture - 4. 컴포넌트 원칙'
category: programming
tags:
  - study
  - clean architecture
---

SOLID 원칙이 벽과 방에 벽돌을 배치하는 방법을 알려준다면, 컴포넌트 원칙은 빌딩에 방을 배치하는 방법을 설명한다.

소프트웨어 컴포넌트가 무엇인지, 컴포넌트를 구성하는 요소는 무엇인지, 이들을 결합하여 시스템을 구성하는 방법에 대해 알아보자

## Chapter 12. 컴포넌트

컴포넌트는 배보단위다. 시스템의 구성요소로 배포할 수 있는 가장 작은 단위다.

자바의 경우 jar 파일, 루비에서는 gem 파일, 닷넷에서는 dll이다. 컴파일형 언어에서는 바이너리 파일의 결합체가 컴포넌트라고 할 수 있다. 

잘 설계된 컴포넌트라면 반드시 독립적으로 배포 가능한, 따라서 독릭적으로 개발 가능한 능력을 갖추어야 한다.

## Chapter 13. 컴포넌트 응집도

어떤 클래스를 어느 컴포넌트에 포함시켜야 할까? 

#### REP: 재사용/릴리즈 등가 원칙 (Reuse/Release Equivalence Principle)

> 재사용 단위는 릴리즈 단위와 같다.

너무 당연해보인다. 컴포넌트가 릴리즈 프로세스를 통해 관리되지 않거나 릴리즈 번호가 부여되지 않는다면 이 컴포넌트를 재사용하고 싶지 않을 것이다.

릴리즈 프로세스를 통해 새로운 버전이 언제 출시되고 무엇이 변했는지 감지할 수 있다. 새로운 릴리즈가 나오면 개발자는 이 릴리즈의 변경사항을 살펴보고 기존 버전을 업데이트 할지 결정한다.

이렇게 릴리즈되는 단일 컴포넌트는 응집성 높은 클래스와 모듈들로 구성되어야 한다. 컴포넌트를 구성하는 모든 모듈은 서로 공유하는 중요한 테마나 목적이 있어야 한다.

하나의 컴포넌트로 묶인 클래스와 모듈은 반드시 함께 릴리즈할 수 있어야 한다. 이들 클래스와 모듈은 버전번호가 같아야 하며, 동일한 릴리즈로 추적관리되고, 동일한 릴리즈 문서에 포함되어야 한다.

#### CCP: 공통 폐쇄 원칙 (Common Closure Principle)

> 동일한 이유로 동일한 시점에 변경되는 클래스를 같은 컴포넌트로 묶어라. 

SOLID의 SRP 원칙을 컴포넌트 관점으로 바라본 것이다. 컴포넌트는 하나의 액터에 대해서만 책임진다는 것이다.

보편적으로 유지보수성은 재사용성보다 훨씬 중요하다. 어플리케이션의 코드가 반드시 변경되어야 한다면, 이러한 변경이 여러 컴포넌트 도처에 분산되어 각각을 수정, 배포해야 하는 것보다 차라리 변경 모두가 단일 컴포넌트에서 발생하는 편이 낫다.

이를 통해 소프트웨어를 릴리즈, 재검증, 배포하는 일과 관련된 작업량을 최소화 할 수 있다.

SOLID의 OCP가 의미하는 수정에 있어서의 폐쇄와도 관련되어 있는데, 100% 폐쇄는 불가능 하므로 변경되는 컴포넌트가 최소화되도록 전략적으로 폐쇄해야 한다.

#### CRP: 공통 재사용 원칙 (Common Reuse Principle)

> 컴포넌트 사용자들을 필요하지 않는 것에 의존하게 강요하지 말라

동일한 컴포넌트로 묶으면 안되는 클래스가 무엇인지에 대한 설명이다.

A 컴포넌트가 B 컴포넌트 내 단 하나의 클래스만을 사용할 수도 있다. 그렇다고 해서 의존성은 조금도 약해지지 않는다. B 컴포넌트가 변경될 때마다 A 컴포넌트도 다시 컴파일, 테스트, 배포해야할 가능성이 있다. 

따라서 A는 B 컴포넌트의 모든 클래스에 의존한다고 볼 수 있다. 

바꿔 말하면 한 컴포넌트에 속한 클래스들을 더 작게 그룹지을 수 없다. 그중 일부 클래스에만 의존하고 다른 클래스와는 독립적일 수 없는 것이다.

따라서 강하게 결합되지 않은 클래스들을 동일한 컴포넌트에 위치시켜서는 안된다.

ISP는 사용하지 않는 메서드가 있는 클래스에 의존하지 말라는 것이라면, CRP는 사용하지 않는 클래스를 가진 컴포넌트에 의존하지 말라는 것에서 관계가 깊다.

#### 컴포넌트 응집도에 대한 균형 다이어그램

REP, CCP는 한 컴포넌트내 클래스의 포함에 관한 규칙이라면, CRP는 배제에 관한 규칙이다. 전자는 컴포넌트를 크게 만들고 후자는 컴포넌트를 작게 만드는 역할을 한다.

뛰어난 아키텍트라면 이 두가지 방향에서의 균형을 이루는 방법을 찾아야 한다.

![ballance_diagram]({{site.url}}{{site.baseurl}}/assets/images/clean_architecture/ca_ballance_diagram.jpeg){: width="400"}

노드은 각 원칙, 간선은 반대편의 원칙을 포기했을 때 감수해야 할 비용이다.

CCP를 포기한다면 사소한 변경이 너무 많은 컴포넌트로 퍼져나갈 수 있다. 반대로 CRP를 포기한다면 한 컴포넌트를 너무 빈번하게 릴리즈해야할 수 있다.

어플리케이션의 생애 주기에 따라 주의를 기울여야하는 원칙은 변할 수 있다. 예를 들면 프로젝트 초기에는 개발가능성이 재사용성보다 중요하기 때문에 CCP가 REP보다 훨씬 중요하다. 

뛰어난 아키텍트는 재사용성과 개발가능성이 상충한다는 것을 이해하고 프로젝트가 원할히 성장할 수 있도록 적절한 균형점을 찾는다.


## Chapter 14. 컴포넌트 결합

이번 장의 3가지 원칙은 컴포넌트 사이의 관계를 설명한다. 마찬가지로 개발 가능성과 설계 사이의 군형을 맞추어야 한다.

#### ADP: 의존성 비순환 원칙 (Acyclic Dependencies Principle)

많은 개발자가 참여하는 큰 프로젝트에서는 어느 누군가의 수정 사항 때문에 그에 의존된 코드들이 하룻밤 사이 갑자기 동작하지 않는 현상이 빈번하다. 이를 일명 숙취증후군이라 한다.

이를 해결하기 위해 `주 단위 빌드`, `의존성 비순환 원칙` 두가지 방법이 제시되었다.

// TODO