---
title: '[기술서적 리뷰] Clean Architecture - 5. 아키텍처 (ch.27~28)'
category: programming
tags:
  - study
  - clean architecture
  - service
  - msa
  - cross-cutting
  - SOLID
  - interface
  - test
  - embedded
---

## Chapter 27. 크고 작은 모든 서비스들

서비스 지향 아키텍처, 마이크로서비스 아키텍처는 최근 큰 인기를 끌고 있다.

- 서비스를 사용하면 상호 결합이 분리되는 것처럼 보인다. 하지만 일부만 맞는 말이다.
- 서비스를 사용하면 개발과 배포 독립성을 지원하는 것처럼 보인다. 이 역시도 일부만 맞는말이다.

#### 서비스 아키텍처?

서비스를 사용한다는 것은 본질적으로 아키텍처에 해당하지 않는다.

시스템의 아키텍처란 의존성 규칙을 준수하며 고수준의 정책을 저수준의 세부사항으로부터 분리하는 경계에 의해 정의된다.

단순히 행위를 분리할 뿐인 서비스라면 값비싼 함수 호출에 불과하다.

함수들의 구성형태도 비슷하다. 시스템에서 아키텍처를 정의하는 요소는 의존성 규칙을 따르며 아키텍처 경계를 넘나드는 함수 호출들이다. 이 외의 나머지 많은 함수들은 행위를 서로 분리할 뿐이다.

서비스도 프로세스나 플랫폼 경계를 가로지르는 함수 호출에 지나지 안흔다. 아키텍처적으로 중요한 서비스, 중요하지 않은 서비스가 존재한다. 

#### 서비스의 이점?

시스템을 서비스들로 분리함으로써 서비스 사이의 결합이 확실히 분리될것으로 예상한다. 

물론 서비스는 개별 변수 수준에서는 각각 결합이 분리된다. 하지만 서로 공유하는 데이터에 의해 이들 서비스는 강하게 결합될 수 있다.

예를 들면 서비스 사이를 오가는 데이터 레코드에 새로운 필드를 추가한다면, 이 필드를 사용해 동작하는 모든 서비스는 반드시 변경되어야 한다. 사전에 서비스 담당 자들은 이 데이터를 해석하는 방식을 사전에 조율해야한다. 따라서 이 서비스들은 서로 결합되어 있다고 할 수 있다.

#### 야옹이 문제

앞에서 보았던 택시 통합 시스템을 다시 생각해보자.

이 시스템은 많은 택시 업체를 알고, 고객은 승차 시간, 비용, 고급 택시 여부, 운전사 경력등 다양한 기준에 따라 택시를 선택하여 승차 요청을 할 수 있다.

확장 가능한 시스템을 구축하고 싶었기에, 팀을 세분화하여 수많은 각각 작은 마이크로 서비스를 개발하고, 유지보수하고, 운영하도록 하였다.

![taxi_system]({{site.url}}{{site.baseurl}}/assets/images/clean_architecture/ca_27_1.png){: width="600"}

고객은 TaxiUI 서비스를 이용해 모바일 기기에서 택시를 호출한다. TaxiFinder 서비스는 여러 TaxiSupplier의 현황을 검토하여 현재 배차 가능한 택시 후보들을 선별한다.

TaxiFinder 서비스는 해당 사용자에게 할당된 데이터 레코드에 후보 택시들의 정보를 저장한다. TexiSelector 서비스는 사용자가 지정한 비용, 시간, 고급 여부등의 조건을 기반으로 후보 택시 중에서 적합한 택시를 선택한다.

이제 TaxiSelector가 TexiDispatcher 서비스로 전달하면, TaxiDispatcher 서비스는 해당 택시에 배차 지시를 한다.

이렇게 시스템을 구축하여 운영해 오던중, 기획팀에서 새로운 서비스 출시 일정을 발표한다.

도시에 야옹이를 배달하는 서비스를 제공해야한다. 사용자는 자신의 집이나 사무실로 야옹이를 배달해달라고 주문할 수 있다.

회사는 도시 전역에 야옹이를 태울 다수의 승차 지점을 설정해야 할것이다. 야옹이 배달 주문이 오면, 근처의 택시가 선택되고, 승차 지점 중 한곳에서 야옹이를 태운 후 올바른 주소로 야옹이를 배달해야한다.

어떤 운전자는 고양이 알러지가 있을 수 있기 때문에 이 서비스에서 제외해야한다. 택시 승객 역시 알러지 정보가 있다면 지난 3일 사이에 야옹이를 배달했던 차량은 배차되지 않아야 한다.

시스템 다이어그램을 다시 살펴보자. 신규 서비스를 위해 어디를 변경해야 할까? 전부다. 

이것이 바로 횡단 관심사(cross-cutting concern)가 지닌 문제다. 모든 소프트웨어 시스템은 이 문제에 직면하게 된다. 현재 설계한 기능적으로 분해된 시스템은 새로운 기능이 기능적 행위를 행단하는 상황에 매우 취약하다.

#### 객체가 구출하다

컴포넌트 기반 아키텍처에서는 SOLID 원칙을 활용하여 다형적으로 확징할 수 있는 클래스 집합을 생성해 새로운 기능을 처리하도록 할 수 있다.

![ca_taxi_system]({{site.url}}{{site.baseurl}}/assets/images/clean_architecture/ca_27_2.png){: width="600"}

클래스들은 기존 서비스의 클래스들과 일치한다. 하지만 그어진 경계와 의존성들이 의존성 규칙을 준수한다는 점도 주목하자.

기존 서비스의 로직 대다수가 이 모델의 클래스들 내부로 녹아들었다. 하지만 택시 배차에 특화된 로직은 Rides 컴포넌트로, 야옹이에 대한 신규 기능은 Kittens 컴포넌트로 들어갔다.

이 두 구체 컴포넌트는 기존 컴포넌트들에 있는 추상 클래스를 오버라이드 한다. 또한 이 컴포넌트 내부의 클래스들은 UI의 제어하에 팩토리(Factories)가 생성한다는 점도 주목하자.

이 전략을 따르더라도 야옹이 기능을 구현하려면 TaxiUI는 어쩔 수 없이 변경해야만 한다. 하지만 그 외의 것들은 변경할 필요가 없다. 야옹이 서비스를 그저 새로 개발하고 런타임에 동적 로드하기만 하면 하면된다.

따라서 야옹이 기능은 기존 택시 서비스와 결합이 분리되며, 독립적으로 개발하여 배포할 수 있다.

#### 컴포넌트 기반 서비스

서비스는 SOLID 원칙대로 설계할 수 있으며 컴포넌트 구조를 갖출 수도 있다. 이를 통해 서비스 내의 기존 컴포넌트를 변경하지 않고도 새로운 컴포넌트를 추가할 수 있다.

![ca_taxi_system_2]({{site.url}}{{site.baseurl}}/assets/images/clean_architecture/ca_27_3.png){: width="600"}

각 서비스의 내부는 인터페이스 클래스의 파생 클래스를 만드는 방식으로 신규 기능을 추가할 수 있다.

#### 횡단 관심사

지금까지 본 내용은 아키텍처 경계가 서비스 사이에 있지 않다는 사실이다. 오히려 서비스를 관통하며, 서비스를 컴포넌트 단위로 분할한다.

시스템이 직면하는 주요 횡단 관심사를 처리하려면, 아래 그림처럼 서비스 내부는 의존성 규칙도 준수하는 컴포넌트 아키텍처로 설계해야 한다.

![ca_taxi_system_dependancy]({{site.url}}{{site.baseurl}}/assets/images/clean_architecture/ca_27_4.png){: width="600"}

TaxiFinder, TaxiSelector 등이 핵심 관심사이고 이들을 가로지르는 Rides, Kittens과 같은 유즈케이스들을 횡단 관심사라고 할 수 있다. (Chapter 16 참고)

#### 결론

서비스는 시스템의 확장성과 개발 가능성 측면에서 유용하지만, 그 자체로는 아키텍처적으로 중요한 요소가 아니다. 시스템의 아키텍처는 시스템 내부에 그어진 경계와 경계를 넘나드는 의존성에 의해 정의된다.

## Chapter 28. 테스트 경계

테스트는 시스템의 일부이며 아키텍처에도 관여한다.

#### 시스템 컴포넌트인 테스트

아키텍처 관점에서는 테스트가 TDD, BDD, Unit Test, Integration Test 등 어떤 방법론을 따르는지 상관없다.

테스트는 세부적이며 구체적이고, 의존성은 항상 테스트 대상이 되는 코드를 향한다. 아키텍처에서 가장 바깥쪽 원으로 생각할 수 있다.

테스트는 독립적으로 배포 가능하다. 시스템 컴포넌트중에 가장 고립되어 있다. 사용자들은 테스트에 의존하지 않으며 테스트의 역할은 운영이 아니라 개발을 지원하는 데 있다.

#### 테스트를 고려한 설계한

테스트의 고립성과, 상용 시스템에는 테스트가 배포되지 않는다는 사실때문에 테스트가 시스템의 설계 범위 밖에 있다고 여긴다.

하지만 테스트가 시스템의 설계와 잘 통합되지 않으면, 테스트는 깨기 쉬워지고, 시스템은 뻣뻣해져서 변경하기 어려워진다.

GUI 테스트 같이 깨지기 쉬운 테스트는 시스템을 뻣뻣하게 만든다. 간단한 변경사항이 대량의 테스트 실패로 이어진다는 사실을 알게되면 개발자는 그러한 변경을 하지 않으려 할 것이다.

이 문제를 해결하려면 테스트를 고려해서 설계해야한다. 소프트웨어 설계의 첫 번째 규칙을 다시 기억하자. 변동성이 있는 것에 의존하지 말라. 

GUI는 변동성이 크다. 시스템과 테스트를 설계할 때 GUI 대신 업무 규칙을 테스트하도록 해야한다.

#### 테스트 API
 
이 목표를 위해서는 테스트가 업무 규칙을 검증하는 데 사용할 수 있도록 특화된 API를 만들면 된다. 

이 API는 인증, 보안 사항을 무시할 수 있으며, 데이터 베이스와 같은 값비싼 자원은 건너뛰고 중요한 업무규칙만 테스트 할 수 있도록 해야한다.

또한 이 API는 어플리케이션의 클래스와 내부 메소드 집합이 어떤 형태인지를 숨겨 테스트 스위트의 집합과 결합되는 것을 방지해준다.