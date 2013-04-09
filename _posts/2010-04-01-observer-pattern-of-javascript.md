---
layout: post
title: "Javascript에서 옵저버 패턴 활용하기"
description: "Javascript에서 옵저버 패턴 활용하기"
category: javascript
tags: [javascript, observer, pattern]
---
{% include JB/setup %}

Observer Pattern이란 여러개의 Class를 작성하였을 때 각 Class들간의 연관성을 유지하기 위해 결합도를 높이는 대신 객체의 상태를 관찰하는 방법을 사용하는 디자인 패턴입니다.

예를 들어 기본적인 MVC 패턴을 사용하여 어플리케이션을 개발하였을 경우 각 Model. View, Controller 클래스들은 각각 다른 역할을 하게됩니다. 그러나 Controller 가 Model과 View의 중심에서 각 객체들에게 명령을 내리면 명령을 받은 객체는 명령의 실행결과나 상태를 Controller에게 보고하여야 합니다.
이때 일반적으로 높은 결합도를 가지게 됩니다. Model이나 View의 입장에선 Controller에게 보고하기 위한 수단으로 Controller의 참조를 취하게 되기 때문이죠

Controller에서 Model의 메소드를 실행
{% highlight javascript %}
    Controller.Model = new Model(Controller);
    Controller.View = new View(Controller);

    Controller.Model.getMemberList();
{% endhighlight %}

Model은 Controller의 참조를 가지고 있으며 요청결과를 Controller에게 알려줌.
{% highlight javascript %}
   Model = function(controller){
      this.controller = controller;
   };
   Model.prototype = {
       //some code blocks
       getMemberList : function(){
             // request Query and Return
             this.controller.printMemberList();
       }
   };
{% endhighlight %}

이런식으로 어플리케이션이 짜여질 경우 Model과 Controller와의 결합도가 매우 높아지기 때문에 재사용성이 떨어지며 유지보수가 힘들어지게 됩니다.

이때 Observer Pattern을 적용하면 이러한 문제점을 어느정도 해소할 수 있습니다.
JavaScript 기반의 Front End 환경에서는 쉽게 예를 들어 DOM Event 모델을 예로 들 수 있겠습니다.

element가 element를 Control 하는 객체를 알 필요가 없음
{% highlight javascript %}
    element.addEventListener("click", Controller.onElementClick);
{% endhighlight %}

일반객체에 Custom Event를 만들자
﻿DOM Event 모델﻿ 이외에도 Observer 패턴은 얼마든지 활용할 수 있습니다.
위에 나왔던 MVC 모델을 예로 들자면.. Model과 View는 Controller의 참조를 취득할 필요 없이..
특정한 Event를 발생시키도록 작성할 수 있습니다.

{% highlight javascript %}
var Controller = function(){
    this.Model = new Model();
    this.View = new View();
    // some code blocks
    this.bindEvents();
    this.Model.getMemberList();
};
Controller.prototype = {
   bindEvents : function(){
       this.Model.addEvent("onGetMemberListComplete",
                         this.printMemberList);
       // some code blocks
   },
   printMemberList: function(data){
       this.View.showMemberList(data);
       // some code blocks
   }
};
{% endhighlight %}


위와 같은 방식으로 하면 Model과 View는 Controller를 따로 알필요 없이 Event 모델만으로 동작을 하게 됩니다. 물론 저기서 나오는 addEvent라는 메소드와 onGetMemberListComplete 라는 이벤트 타입은 제가 만들어 낸것이죠.  
그럼 어떻게 만드냐구요?? 제가 만든 Observer Class를 공개합니다.

__Observer.js__
{% highlight javascript %}
 /*!
 * JavaScript Observer
 *
 * Date: 2010. 4. 1.
 * Author: boxersb(Kyoungtaek Koo)
 * blog: http://boxersb.github.io
 * twitter: @boxersb
 * gmail: boxersb@gmail.com
 *
 * Copyright (c) 2010 boxersb (Kyoungtaek Koo)
 */
var Observer = {
  EVENTID : 0,
  addEvent : function(type, hnd){
    if(!this.listeners) this.listeners = {};
    if(!this.listeners[type]) this.listeners[type] = {};
    
    var eventId = Observer.EVENTID++;
    this.listeners[type][eventId] = hnd;
    
    return eventId;
  },
  
  fireEvent : function(type){
    if(!this.listeners || !this.listeners[type]) return false;
    var handlers = this.listeners[type], eventId,
      args =  Array.prototype.slice.call(arguments);
    if(handlers.stop) return false;
    
    args.shift();
    for(eventId in handlers) if(handlers.hasOwnProperty(eventId)){
      if(eventId !== "stop"){
        if(!handlers[eventId].stop){
          handlers[eventId].apply(this, args);
        }
      } 
    };
  },
  
  removeEvent : function(type, hnd){
    if(!this.listeners || !this.listeners[type]) return -1;
    var handlers = this.listeners[type], eventId = -1;
    if(typeof hnd === "function"){
      for(eventId in handlers) if(handlers.hasOwnProperty(f)){
        if(handlers[eventId] === hnd){
          delete handlers[eventId];
          break;
        } 
      };
      return !handlers[eventId];
    }else{
      if(handlers[hnd]) delete handlers[hnd]
      return !handlers[hnd];
    };
  },
  
  stopEvent : function(type, hnd){
    if(!this.listeners || !this.listeners[type]) return -1;
    var handlers = this.listeners[type], eventId = -1; 
    if(hnd){
      if(typeof hnd === "function"){
        for(eventId in handlers) if(handlers.hasOwnProperty(f)){
          if(handlers[eventId] === hnd){
            handlers[eventId].stop = true;
          }
        };
      }else{
        handlers[hnd].stop = true;
      }
    }else{  
      handlers.stop = true;
    }
  },
  
  restoreEvent : function(type, hnd){
    if(!this.listeners || !this.listeners[type]) return -1;
    var handlers = this.listeners[type], eventId = -1;
    if(hnd){
      if(typeof hnd === "function"){
        for(eventId in handlers) if(handlers.hasOwnProperty(f)){
          if(handlers[eventId] === hnd){
            handlers[eventId].stop = false;
          }
        };
      }else{
        handlers[hnd].stop = false;
      }
    }else{
      handlers.stop = false;
    }
  },
  
  applyObserver : function(tclass){
    for(var p in this){
      if(this[p] !== arguments.callee){       
        tclass.prototype[p] = this[p];
      }
    };

    return true;
  }
};
{% endhighlight %}

위 Observer 객체를 사용하여 Event 모델을 구축합니다.
{% highlight javascript %}
    // Controller definitions...
   Observer.applyObserver(Controller);
   Model = function(){
       // some code blocks
   };
   Model.prototype = {
       //some code blocks
       getMemberList : function(){
             // request Query and Return
            var data = xhr.responseXML;
             this.fireEvent("onGetMemberListComplete", data);
       }
   };
   Observer.applyObserver(Model);
{% endhighlight %}

* __addEvent__(_type, handler_) - 관찰(Observe)할 이벤트를 추가한다. 이벤트 타입은 자유롭게 설정.
* __fireEvent__(_type, args.._) - 관찰하고 있는 이벤트 type의 모든 헨들러를 호출한다. 
* __removeEvent__(_type, handler_) - 특정 이벤트 옵저버를 제거한다.
* __stopEvent__(_type, handler_) - 특정 이벤트 옵저버를 일시적으로 정지시킨다(제거하지 않음)
* __restoreEvent__(_type, handler_) - 정지된 특정 이벤트 옵저버를 복원한다.
* __applyObserver__(_tclass_) - 대상 클래스에 Observer API를 확장한다.
