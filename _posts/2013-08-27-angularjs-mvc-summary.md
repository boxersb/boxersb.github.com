---
layout: post
title: "AngularJS의 MVC 패턴 개요"
description: ""
category: angularJS
tags: [angular, javascript, MVC]
---
{% include JB/setup %}

## Angular의 MVC

모델-뷰-컨트롤러(MVC)가 처음 나온 이후로 수년간 여러가지 다른 의미로 변화되고 있는 와중에, Angular는 본래의 MVC 소프트웨어 디자인 패턴 이면의 기본 원칙들을 클라이언트 웹 애플리케이션 개발에 적용하고자 했다.

MVC 패턴을 요약하자면:

* 애플리케이션은 개별적인 표현과 데이터, 로직 컴포넌트 단위로 분리한다.
* 이런 컴포넌트간의 약한 결합성을 장려한다.

서비스와 의존성 주입 개념을 통해, MVC는 angular 애플리케이션을 더 구조적이고 유지보수를 쉽게해주며, 더 testable 하게 만들어준다.

아래의 주제들은 angular가 angular 특유의 웹 애플리케이션 개발 방법론에 어떤식으로 MVC 패턴을 적용했는지 설명한다.

* Model 컴포넌트의 이해
* Controller 컴포넌트의 이해
* View 컴포넌트의 이해

### Model 컴포넌트의 이해

이 문서의 맥락에 따라, ___모델___이라는 용어는 하나의 엔티티(예를 들어, 휴대폰 목록의 배열을 값으로 가지는 "phones"라고 불리는 모델)를 나타내는 단일 객체이거나 애플리케이션의 전체 데이터 모델 (모든 엔티티)중 하나를 가리킨다.

Angular에서 모델은 angular Scope 객체의 프로퍼티로 접근 가능한 데이터를 말한다. 프로퍼티의 이름은 모델 식별자이며 해당 값은 모든 형태의 자바스크립트 객체이다. (원시타입의 값와 배열을 포함한다)

Angular에서 자바스크립트 객체를 모델로 만들기 위한 요구사항은 객체가 scope 객체의 프로퍼티로써 Angular 스코프에 의해 참조되어야 한다는점 뿐이다. 이 프로퍼티 참조는 명시 또는 묵시적으로 생성할 수 있다.

명시적으로 scope 프로퍼티들이 자바스크립트 객체를 참조하여 모델을 생성하는 방법은 다음과 같다.:
* 코드상에서 scope 객체에 프로퍼티를 직접 할당한다. 컨트롤러에서 가장 일반적으로 사용될 수 있는 방법이다.

```js
function MyCtrl($scope) {
    // create property 'foo' on the MyCtrl's scope
    // and assign it an initial value 'bar'
   $scope.foo = 'bar';
}
```

* 템플릿에서 할당 연산자를 통한 angular 표현식을 사용한다.

	```html
	<button ng-click="{{foo='bar'}}">Click me</button>
	```

* 템플릿에서 ngInit 지시자(directive)를 사용한다. 이 방법은, 연습용으로만 사용하고 실 서비스에서는 지양하길 바란다.

	```html
	<body ng-init=" foo = 'bar' "> ... </body>
	```

Angular는 다음의 템플릿을 생성하면 묵시적으로(scope 프로퍼티 생성과 적절한 값을 할당함으로써) 모델을 생성한다.

* input과 select, textarea 등의 폼 요소

	```html
	<input ng-model="query" value="fluffy cloud">
	```

	위 코드는 현재 스코프에 "query"라는 모델을 생성하고 "fluffy cloud"라는 값을 설정한다.
* ngRepeater를 통한 반복 선언시

	```html
	<p ng-repeat="phone in phones"></p>
	```

    위 코드는 "phones" 배열내 각 요소를 위한 하나의 자식 스코프를 생성하고, 각 스코프에 배열 원소의 값인 "phone" 객체 모델을 생성한다. 

Angular에서는 아래의 경우에 자바스크립트 객체를 모델로 만들지 않는다.

* Angular 스코프에 객체를 참조하는 프로퍼티가 없는경우
* 가비지 컬렉션에 들어갈 수 있는 객체를 참조하는 프로퍼티를 포함한 모든 Angular 스코프

아래의 그림은 간단한 템플릿을 통해 데이터 모델이 묵시적으로 생성되는 모습을 보여준다.
![Model created implicitly from a simple Template](/imgs/about_model_final.png)

