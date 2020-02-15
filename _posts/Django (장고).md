# Django (장고)

> [파이썬 웹 프로그래밍 실전편(개정판): Django(장고)를 활용한 쉽고 빠른 웹 개발]
>
> [RunningWater](https://justmakeyourself.tistory.com/entry/django-mvt-pattern)

* MVT 개발 방식

Django의 MVT 방식은 자바 웹 프로그래밍의 MVC 방식과 거의 동일한 개념으로 웹 프로그래밍의 Model, View, Template의 3가지 영역으로 나누어 개발하는 방식.

Model: 테이블을 정의. 쉽게 표현하면 데이터 관련. (MVC 방식의 Model)

View: application의 제어 흐름 및 처리 로직을 정의. 쉽게 표현하면 로직(url) 관련. (MVC 방식의 Controller)

Template: 사용자가 보게 될 화면의 모습을 정의. 쉽게 표현하면 UI관련. (MVC 방식의 View)



* MVT 코딩 순서

MVT 방식에 따르면 화면 설계는 View와 Template 코딩으로 연결되고, 테이블 설계는 Model 코딩에 반영됨. 따라서 독립적으로 개발할 수 있는 Model을 먼저 코딩하고, View와 Template은 서로 영향을 미치므로 Model 이후에 코딩하는 것이 일반적임.

View와 Template 중에서 코딩의 우선 순위는 개인에 따라 다름.



~~말이 너무 길면 무슨 소리인지 와닿지 않으므로 이제 시작하겠음.~~



# DJango 실행

묻지도 따지지도 말고 아래의 순서까지는 따라하시면 됨.



1. `pip install django`: (가상환경에) Django 설치
2. `cd ./'원하는 경로'`: 원하는 경로로 이동. 3번 명령 실행 시 '원하는 경로'에 (프로젝트) 폴더가 생성되므로 필요시 이동.
3. `django-admin.py startproject '프로젝트명'`: '프로젝트명' (ex. mysite)으로 폴더가 생성됨. 생





