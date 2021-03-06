---
layout: post
title:  "팩토리 패턴"
date:   2017-03-25 01:30:13 +0800
categories: Programming DesignPattern
tags: KR C++
---
> 이 포스팅은 __Head First Design Patterns__ 를 참고하여 작성하였습니다.  

## __팩토리 패턴이란?__
* 객체를 생성하기위한 인터페이스를 정의할 때 어떤 클래스의 인스턴스를 만들지에 대해서는 서브 클래스에서 결정하게 만드는 ```디자인 패턴```이다.
  + 이 패턴을 이용하면 new의 기능을 서브 클래스한태 맡기는 것이다.

> 다른 종류로는 추상 팩토리 패턴도 있는데, 인터페이스를 이용하여 서로 연관되거나 의존하는 객체를 구상 클래스를 지정하지 않고 생성한다.

## __팩토리 패턴은 어떤 경우에 적용될까?__
특정 상황별로 다른 클래스를 생성해야할때 이 패턴을 사용한다. 
예를들어 피자공장에서 피자를 만들 때 주문에 따라 치즈 피자, 페페로니 피자, 불고기 피자를 만든다고 하면,
공장은 주문에 따라 피자를 내놓아야할 것 이다. 만약, 치즈 피자 주문이 들어오면 치즈 피자를 만들어야하고,
다른 피자가 들어올 때마다 그때 그때 다른 피자를 내놓아야한다.
이런 상황에서 팩토리 패턴을 사용할 수 있다.

## __팩토리 패턴을 구현해보자__
이번 개발할 프로그램은 피자 공장에 들어갈 피자 공장 프로그램이다.

* __초기 요구 사항__
  + 사용자가 요청하는대로 피자를 주어야한다.
  + 치즈, 불고기, 페페로니 세 종류가 있다.

그럼 개발을 해보면, 단순하게는 특정 상황이 들어오면 특정 피자를 만들어서 반환하는 식으로 개발을 할 수 있을 것 이다.

[그림]

이렇게 되면, 코드 수정이나 확장을 해야할 때 매번 코드를 수정/제거 해야해서 유지보수가 귀찮을 것 이다.

- - -

자 그럼 __데코레이터 패턴__ 을 활용하여 코드를 수정해보자.

### 디자인 원칙
1. OCP(Open close principle) 즉, 클래스는 확장에 대해서는 열려있어야 하지만, 코드 변경에 대해서는 닫혀 있어야한다.

* __부모 클래스 (Duck)__
  + fly()
    - 못 나는 오리도 있다.
  + quack()
    - 오리별로 소리가 다르다.
  + swim()
    - 나중에는 물에 뜨지 않는 오리가 생길 수 있지 않을까?

위와 같이 크게 그룹을 만들어서 쪼개었다. 이제 각 그룹별로 기능을 캡슐화 해보자.
* __FlyBehavior__
  + 날 수 있다!
  + 날지 못한다...
* __QuackBehavior__
  + 꿱꿱!
  + 삑삑!
  + 조용...
* __SwimBehavior__
  + 수영 한다!
  + 수영 못한다...

[그림]

이제 우리는 복사/붙여넣기가 아니라 각 그룹에 기능을 구현해서 넣으면 된다. 전보다 훨씬 편해지지 않았는가? 이런식으로 두 클래스를 합치는 것을 구성(Composition)을 이용하는 것 이라고 하는데, 여기의 여러 클래스는 행동을 상속 받는 대신, 올바른 객체로 구성되므로써 행동을 부여받게된다.

## __마지막__
내가 이해한 것을 바탕으로 마지막으로 한 가지 예를 들자면, 만약 어떤 게임에서 이동한다라는 행위는 같은데 복도에서는 걸을 수 밖에 없고, 바깥에서는 뛸 수밖에 없고... 이런 한 행동한다라는 그룹내에서 패턴을 바꿀 때 유용한 것 같다. 

스트래티지 패턴은 수정/추가를 해야하는 부분이 오버라이딩한 모든 부분을 수정하도록 만드는 경우에 추가하면 편하게 작업할 수 있다. 