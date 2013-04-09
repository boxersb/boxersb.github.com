---
layout: post
title: "Object의 toString과 toValue 메서드에 대한 고찰"
description: "Object의 toString과 toValue 메서드에 대한 고찰"
category: javascript
tags: [javascript, object, toString, toValue]
---
{% include JB/setup %}

[Ben Cherry]: http://twitter.com/bcherry
[Ben Cherry]는 [magic of Object - primitive conversions](http://www.adequatelygood.com/2010/3/Object-to-Primitive-Conversions-in-JavaScript) 에서 JavaScript의 toString과 toValue 메소드를 이용한 기본 객체형변환에 대해 심도있게 다루었습니다.

JavaScript에서 특정객체를 alert 혹은 console 에서 확인을 해보면 보통 다음과 같은 결과를 보여줍니다.
{% highlight javascript %}
alert( { } );           // [object Object]
alert( function(){} );  // function(){}
alert( 1 );             // 1
alert( "hello" );       //  hello
alert( true );          //  true
alert( [1, 2, 3] );    // 1,2,3
{% endhighlight %}
  
  
JavaScript의 모든 객체는 toString 과 toValue 메소드를 갖습니다.
위에 나열된 alert 혹은 console 에서 확인한 결과는 내부적으로 호출된 해당 객체의 toString 메소드의 결과입니다.

toValue 메소드는 toString과 비슷해 보이지만 각종 연산자에서 사용되었을 경우 객체의 결과 값을 반환하도록 디자인되어 있습니다.
{% highlight javascript %}
alert( { } + { } );                    // [object Object][object Object]
alert( function(){} + function(){} );  // function(){}function(){}
alert( 1 + 1);                         // 2
alert( "hello" + "world" );            //  helloworld
alert( true + true );                  //  2
alert( [1, 2, 3] + [4, 5, 6] );        // 1,2,34,5,6
{% endhighlight %}

아래 toString과 toValue를 확장해서 쓰는 좋은 예제가 있습니다.
{% highlight javascript %}
function population(country, pop) {
    return {
        country: country,
        pop: pop,
 
        toString: function () {
            return "[Population " + 
                "\"" + country + "\" " +
                pop +
            "]";
        },
 
        valueOf: function () {
            return pop;
        }
    };
}
 
var america_pop = population("USA", 350e6);
var mexico_pop = population("Mexico", 200e6);
var canada_pop = population("Canada", 200e6);
 
alert(america_pop); // [Population "USA" 350000000
 
var north_america_pop = america_pop + mexico_pop + canada_pop;
 
alert(north_america_pop); // 750000000
{% endhighlight %}


JavaScript의 기본 데이터타입 이외에도 개발자가 직접 개발한 Custom 객체도 toString과 toValue 메소드를 적절히 오버라이딩하면 편리하게 묵시적인 형변환을 사용할 수 있습니다. 해당객체가 어떤 작업을 수행하는 객체인지에 대한 설명(toString)으로 활용할 수도 있고, 해당 객체의 동작상태에 대한 값(toValue)로써도 사용할 수 있습니다.

다음은 toString, toValue를 잘못 활용한 예입니다.
{% highlight javascript %}
var foo = {
    toString: function () {
        return 5;
    },
    valueOf: function () {
        return "foo";
    }
};
alert(foo.toString() + 1); // 6 (bad!)
alert(foo + 1); // "foo1" (no good!)
alert(+foo); // NaN (the worst!)
{% endhighlight %}


[Ben Cherry]는 위 묵시적 형변환에 대한 성능적인 검증도 마쳤습니다.
toString, toValue를 적절히 활용하면
재사용 가능한 객체를 디자인할때 좀 더 깔끔한 코드를 작성할 수 있습니다.
단지 alert이나 console로 찍어내는것 만으로도 객체의 상태를 알게 할수 있습니다. 즉 디버깅이 쉬워집니다.
또한 모든 브라우저에서 ECMA명세에 의해 위 내용을 구현하고 있어 호환성 이슈가 없습니다.

그러나 성능적인 측면에서는 대부분의 브라우저가 alert(america_pop) 처럼 묵시적인 toString 메소드 호출보다는 alert(america_pop.toString()) 과 같이 명시적으로 호출하여 객체를 확인하는 것이 빠른것으로 나타났습니다. 아래는 각 브라우저별 묵시적 변환과 명시적 변환법을 사용한 성능비교를 위해 1,000,000번 같은 내용을 반복 호출한 결과표 입니다.

* __Firefox 3.6.2__ : 874ms vs 320ms - 약 세배빠름
* __Chrome5__ : 94ms vs 47ms  - 약 두배빠름
* __Opera 10.50__ : 115ms vs 182ms - 약간더 느림
* __Safari 4__ : 409ms vs 280ms - 약 두배 빠름
* __IE8__ : 2856ms vs 2786ms - 거의 비슷함
* __IE9(preview)__ : 645ms vs 633ms - 거의 비슷함




출처 :  
> [http://ajaxian.com/archives/a-very-detailed-look-at-object-to-primitive-conversions](http://ajaxian.com/archives/a-very-detailed-look-at-object-to-primitive-conversions)  
>[http://www.adequatelygood.com/2010/3/Object-to-Primitive-Conversions-in-JavaScript](http://www.adequatelygood.com/2010/3/Object-to-Primitive-Conversions-in-JavaScript)

원문출처를 바탕으로 재구성한 포스트입니다.