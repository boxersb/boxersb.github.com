---
layout: post
title: "Jekyll 소개"
description: "Jekyll 소개"
category: jekyll
tags: [jekyll, git, github, pages]
---
{% include JB/setup %}


## Jekyll(지킬) 이란?
Jekyll은 Ruby Gem으로 제공되며 템플릿과 템플릿의 구성요소, 인라인 코드, 마크다운과 같은 동적인 구성요소를 정적인 웹페이지로 만들어주는 파싱 엔진이다.

이 웹사이트도 Jekyll로 만들어져 있다.
그 밖의 다른 Jekyll 웹사이트는 [여기](https://github.com/mojombo/jekyll/wiki/Sites)서 살펴볼 수 있다.
<br />
<br />

## Github Pages와 Jekyll을 이용한 블로그 만들기
Github Pages와 Jekyll을 사용하면 git 기반 블로그를 만들 수 있다.  
rss는 물론 Atom도 지원하며, disqus나 facebook 소셜 플러그인을 사용하면, 댓글 기능도 활성화 할 수 있다.  

### Jekyll 설치
Jekyll은 Ruby gem으로 제공되므로 커멘드 라인에서 아래와 같이 설치한다.

{% highlight bash %}
$ gem install jekyll
{% endhighlight %}

### Jekyll-Bootstrap 설치
먼저, [Github](https://github.com)에서 SITENAME.github.com 과 같은 이름의 저장소를 만든다.  
SITENAME은 만들려는 사이트의 이름이다.  

다음으로, Jekyll-Bootstrap을 설치한다.  
Jekyll-Bootstrap은 Jekyll을 사용한 사이트를 만들기위해 필요한 작업을 간단하게 처리해준다.  

{% highlight bash %}
$ git clone https://github.com/plusjade/jekyll-bootstrap.git SITENAME.github.com
$ cd SITENAME.github.com
$ git remote set-url origin git@github.com:YOURACCOUNT/SITENAME.github.com.git
$ git push origin master
{% endhighlight %}

앞서 만들어 두었던 SITENAME.github.com 저장소에 jekyll-bootstrap을 clone 한다.  
그런 다음, git remote set-url 을 사용하여 origin을 다시 SITENAME.github.com으로 수정한다.  
마지막으로, clone 받아둔 jekyll-bootstrap 파일들까지 원격 저장소에 push 한다.  
  
여기까지 수행하면, 블로그를 만들기위한 기본적인 jekyll 설정이 끝난셈이다. ___Very Simple!!___  

### 로컬 서버 띄우기
jekyll은 로컬 웹서버도 지원한다.

{% highlight bash %}
$ jekyll --server
{% endhighlight %}

웹 서버를 시작하면, 현재 사이트에서 변경된 파일들을 모아, static html 파일들을 자동으로 생성해준다.  
브라우저에서는 http://localhost:4000 으로 방금 만든 사이트를 테스트해볼 수 있다.

### 테마 사용하기
jekyll은 테마기능도 지원한다. [Jekyll 공식 테마 살펴보기](http://themes.jekyllbootstrap.com/)  
테마는 여러개를 하나의 Jekyll 사이트에 설치할 수 있으며, 테마 스위칭 기능도 지원한다. 물론, 테마는 커스터마이징이 가능하다.

**테마 설치하기**
{% highlight bash %}
$ rake theme:install git="https://github.com/jekyllbootstrap/theme-the-program.git"
$ rake theme:install git="https://github.com/boxersb/jekyll-theme-slate.git"
{% endhighlight %}

**테마 스위칭**
{% highlight bash %}
$ rake theme:switch name="the-program"
{% endhighlight %}  
  
  
## Jekyll 설정하기
Jekyll 사이트에 대한 설정은 /\_config.yml 파일에 수록된다.  
여기서는, 포스트의 permalink 스키마 변경이나, 사이트 소유자 정보, 사이트명, 코멘트나 분석툴 같은 외부 도구등을 설정할 수 있다.  

### 댓글 사용하기
\_config.yml 파일에서 comments 절의 내용을 수정하면 된다.  
여기서는 facebook 소셜 플러그인을 사용하였다. 각 댓글 서비스별로 수정이 필요한 곳은 FIXME로 표시해둔 부분을 참조하면 된다.  

{% highlight ruby %}
comments :
  provider : facebook	#FIXME
  disqus :
    short_name : abc	#FIXME
  livefyre :
    site_id : abc	#FIXME
  intensedebate :
    account : abc	#FIXME
  facebook :
    appid : abc		#FIXME
    num_posts: 5
    width: 640
    colorscheme: light
{% endhighlight %}

### 사이트 분석툴 사용하기
\_config.yml 파일에서 analytics 절의 내용을 수정하면 된다.  
여기서는 Google Analytics를 사용하였다.  

{% highlight ruby %}
analytics :
    provider : google 
    google : 
        tracking_id : 'UA-123-12'	#FIXME
    getclicky :
      site_id : abc			#FIXME
    mixpanel :
        token : '_MIXPANEL_TOKEN_'	#FIXME
{% endhighlight %}



## 블로깅하기
Jekyll + Github Pages 를 활용한 블로깅은, 기본적으로 마크다운 파일을 만들고(rake를 활용한 명령어가 지원된다), jekyll로 파싱한다음, 해당 git 저장소의 master 브랜치에 push 해야 정상적으로 Deploy가 된다.
매우 개발스러운 방법이지만, Rich Text Editing을 싫어하며 웹 호스팅하기 아까운 나같은 사람한테는 딱 좋은 플랫폼인 셈이다.  

### 새 포스트 만들기
{% highlight bash %}
$ rake post title="Hello World"
{% endhighlight %}

이렇게 rake를 사용한 명령어를 사용하면 지정된 포스트에 해당되는 마크다운 파일이 만들어진다.  

> *Creating new post: ./_posts/2013-04-03-hello-world.md*

해당 마크다운 파일을 열면, 다음과 같은 기본 템플릿이 생성되었음을 알 수 있다.  

> \-\-\-\-  
> layout: post  
> title: "Hello World"  
> description: ""  
> category:   
> tags: \[\]  
> \-\-\-\-  
> \{ % include JB/setup % \}

대충 감이 왔겠지만, title과 description, tags 절을 적절히 커스터 마이즈하고, \{ % include JB/setup % \} 이후 부터 markdown 문법을 사용하여 post를 작성하면 된다.  

### 페이지 만들기
포스트 외에 페이지도 만들 수 있다.  

{% highlight bash %}
$ rake page name="about.md"
$ rake page name="pages/profile.md"
$ rake page name="pages/blogs"
{% endhighlight %}

위와 같은 명령어를 실행한 후 결과메세지를 참고해서 해당 파일을 수정하면 된다.  

### 배포하기
jekyll --server를 사용하여 로컬 웹서버로 생성한 페이지 및 포스트를 모두 확인했다면, 실제로 웹에 노출되도록 배포해야한다.  
Git 페이지 기반이며, Github Pages에서는 저장소의 master 브랜치에 해당되는 내용을 표시하므로, 간단히 master 브랜치로 변경사항을 push 하기만 하면 된다.  

{% highlight bash %}
$ git add .
$ git commit -m "new post(Jekyll 소개)"
$ git push origin master
{% endhighlight %}






