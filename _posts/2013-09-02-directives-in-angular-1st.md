---
layout: post
title: "AngularJS의 Directives"
description: ""
category: angularJS
tags: [angular, javascript, MVC, directives]
---
{% include JB/setup %}


서적 번역하던 영향으로(-_-;) 앞선 글에서는 Directives를 "지시자"로 번역했었는데, 워낙 자주 등작하며 중요한 개념이므로 혼란을 없애기 위해 이 글에서는 directive 라는 원 단어를 그대로 인용하도록 하겠다.


http://docs.angularjs.org/guide/directive

Directive는 새로운 HTML 트릭을 만드는 방법이다. Directive는 DOM이 파싱되는 동안 HTML에 매칭 및 실행된다. 즉, directive는 DOM의 변형이나 동작방식을 등록할 수 있게 한다.

Angular는 웹 애플리케이션을 만드는데 유용한 자체 directive들을 내장하고 있지만, 마치 HTML이 선언적 도메인 특정 언어(DSL) 처럼 사용할 수 있도록 확장할 수도 있다.

## HTML로 부터 directive 호출하기

Directive는 _ngBind_처럼 케멀 케이스 형태의 이름을 가진다. directive는 케멀 케이스의 이름을 : 나 -, _등의 특수 문자를 사용한 스네이크 케이스로 변환함으로써 호출할 수 있다. 부가적으로 HTML 유효성 검사를 통과하기 위해서는, x- 나 data-와 같은 접두어를 사용하여 directive를 만들 수도 있다. ng:bind나 ng_bind, x-ng-bind, data-ng-bind 와 같은 형태 모두 directive로 사용 가능하다.

Directive는 HTML 요소 테그명이나, 속성명, 클래스명은 물론, 주석으로도 사용 가능하다. 아래의 예제는 _myDir_ 이라는 directive를 호출하는 동일한 코드이다. (그러나, 대부분의 directive들은 속성에 국한되게 만드는 경우가 많다.)

```html
{% raw %}
<span my-dir="exp"></span>
<span class="my-dir: exp;"></span>
<my-dir></my-dir>
<!-- directive: my-dir exp -->
{% endraw %}
```

Directive는 매우 다양한 방법으로 호출할 수 있지만, 최종적인 결과는 다음의 예제와 같다.

### 소스코드

### 데모

## 텍스트 및 속성 바인딩
Directive의 해석 단계에서 컴파일러는 $interpolate 서비스를 사용하여 HTML에 삽입된 표현식이 있는지를 판단하고, 해당 표현시에 텍스트와 속성을 매칭시킨다. 이 표현식들은 watches로써 등록되며 일반적인 digest 사이클의 일부로써 갱신된다. 다음은, HTML 보간(interpolate)의 예제이다.

```html
{% raw %}
<a ng-href="img/{{username}}.jpg">Hello {{username}}!</a>
{% endraw %}
```
	
## ngAttr 속성 바인딩
'ng-attr-' 또는 'ng:attr-'로 보간되는 ngAttr 접두어를 사용한 속성을 바인딩하였다면, 해석단계에서 해당 접두어가 제거되고 일반 속성으로 바인딩된 값이 적용된다. 이렇게 하면 브라우저에 의해서 미리 처리되는 예외 속성들 (e.g img[src] 또는 svg의 circle[cx] 속성)에도 바인딩을 적용할 수 있다.

예를들어, 다음과 같이 템플릿을 작성하고

```html
{% raw %}
<svg>
  <circle ng-attr-cx="{{cx}}"></circle>
</svg>
{% endraw %}
```
	
모델 cx에 값을 5로 할당하면, 최종적으로 DOM은 아래와 같이 렌더링된다.

```html
<svg>
  <circle cx="5"></circle>
</svg>
```
	
{% raw %}
{{cx}}를 cx 속성에 직접적으로 연결하면, 다음과 같은 오류가 발생한다.: _Error: Invalid value for attribute cx="{{cx}}"._
{% endraw %}
_ng-attr-cx_ 를 사용하면 이 문제를 해결할 수 있다.

## HTML 해석 단계 및 directive 매칭
[$compile]:http://docs.angularjs.org/api/ng.$compile "$compile"

HTML의 해석 과정은 세 단계로 발생한다.

1. 먼저, 표준 브라우저 API를 사용하여 HTML을 DOM으로 파싱한다. 템플릿은 HTML로 파싱 가능해야하므로, 이것을 이해하는것이 중요하다. 대부분의 템플릿 시스템은 DOM이 아니라 문자열을 사용하여 운용된다.
2. DOM의 해석은 [$compile()][$compile] 메서드를 호출함으로써 실행된다. 이 메서드는 DOM을 탐색하여 directive를 찾는다. directive를 찾으면, 해당 DOM 요소에 조합된 directive의 목록에 해당 사항을 추가한다. 해당 DOM 요소에 대한 모든 directive가 식별되면, 해당 directive의 목록은 우선순위에 따라 정렬되고, 각 directive에서 정의한 compile() 함수를 실행한다. directive 컴파일 함수를 실행함으로써 해당 DOM 구조를 조정하여, 다음 단계인 link() 함수로 결과를 넘길 수 있게 한다. [$compile()][$compile] 메서드는 개별적인 directive 컴파일 함수로부터 반환된 모든 linking 함수의 목록을 조합하여 반환한다.
3. 이전 단계에서 반환된 linking 함수를 호출함으로써 템플릿과 스코프를 연결한다. 개별적인 directive의 linking 함수를 호출함으로써 요소의 이벤트 리스터를 등록하고 해당 scope의 watches를 설정할 수 있게 된다. 결과적으로 scope와 DOM간의 실시간 바인딩이 이루어진다. scope의 변경은 DOM에 즉시 반영된다.

```js
var $compile = ...; // injected into your code
var scope = ...;
 
var html = '<div ng-bind="exp"></div>';
 
// Step 1: parse HTML into DOM element
var template = angular.element(html);
 
// Step 2: compile the template
var linkFn = $compile(template);
 
// Step 3: link the compiled template with the scope.
linkFn(scope);
```
	
## compile과 link를 분리한 이유
여기서는 해석 단계가 compile과 link 과정으로 분리된 이유에 대해 설명한다. 이 부분을 이해하기 위해, 실제적인 repeater 예제를 살펴보자.

```js
{% raw %}
Hello {{user}}, you have these actions:

<ul>
  <li ng-repeat="action in user.actions">
    {{action.description}}
  </li>
</ul>
{% endraw %}
```

쉽게 말해서 compile과 link 과정으로 분리된 이유는 repeater처럼 언제라도 모델이 변경될때 DOM 구조도 변경될 수 있게 하기 위함이다.

위 예제가 해석될때, 컴파일러는 모든 노드를 방문하여 directive를 찾는다. {% raw %}{{user}}{% endraw %} 가 보간 directive의 예이다. _ngRepeat_는 또 다른 directive다. 그러나, _ngRepeat_에는 모순이 있다. 해당 directive는 _user.actions_의 모든 _action_ 항목에 따른 새로운 목록을 빠르게 찍어낼 필요가 있다. 즉, 요소를 빠르게 복제하기 위해서는 li 요소의 깨끗한 사본이 필요하며, 새 _action_이 삽입됨에따라, 템플릿의 li 요소가 복제되어 ul에 삽입되어야 한다. 그러나, li 요소를 복제하는것 만으론 충분하지 않다. 마찬가지로 li를 해석하여 {% raw %}{{action.description}}{% endraw %}과 같은 directive를 올바른 scope과 연결해야한다. 메서드 본연의 기능은 li 요소의 복사본을 삽입한 다음 해석할 것이지만, 해석시 DOM 트리르 탐색하고 directive를 찾아서 실행하는 한, 모든 li 요소의 사본을 해석한다는것은 느릴 수 밖에 없다. repeater 내부에 100개의 항목에 대한 빠른 해석 과정을 포함하길 원한다면, 성능문제에 직면하게 된다.

해결 방법으로 해석 단계를 두개의 과정으로 나눈다.  
compile 과정은 모든 directive들을 식별하고 우선순위에 따라 정렬하며, linking 과정은 스코프의 특정 인스턴스와 li의 특정 인스턴스를 연결한다.

_ngRepeat_는 li 요소 하위로의 해석 단계 진행을 막으며 동작한다. 대신, _ngRepeat_ directive는 li를 분리하여 해석한다. li 요소 해석 단계의 결과는 li 요소내 포함된 모든 directive들을 포함하고 있는 linking 함수이며, DOM에 추가될 준비가 된 특정 li 요소의 사본이다. 실행시점에 _ngRepeat_는 표현식을 감시하여 배열에 항목이 추가될때 li 요소를 복제하고, li 요소 사본을 위한 새로운 스코프를 생성하며, li 요소 사본의 link 함수를 호출한다.

요약하자면

* compile 함수 - compile 함수는 대부분의 direcvie들이 DOM 요소로 변환하는 템플릿과는 달리 특정 DOM 요소와 함께 동작하는것을 염두에 두고 작성되기 때문에 directive와 매우 관련이 깊다. directive의 인스턴스 사이에서 공유가 가능한 모든 작업들은 성능상의 이유로 compile 함수 내부로 이동하여야 한다.
* link 함수 - 
l
ink function - It is rare for the directive not to have a link function. A link function allows the directive to register listeners to the specific cloned DOM element instance as well as to copy content into the DOM from the scope.

	





