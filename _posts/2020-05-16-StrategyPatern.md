---
layout: post
title:  "스트래티지 패턴"
date:   2020-05-06 09:15:00 +0800
categories: Programming DesignPattern
tags: KR C++
---
> 이 포스팅은 __Head First Design Patterns__ 를 참고하여 작성하였습니다.  

#### __스트래티지 패턴이란?__
* 각 기능을 클래스로 캡슐화하는 ```디자인 패턴``` 이다.
  + 문제를 해결할 수 있는 여러 알고리즘(기능)을 그룹내에서 캡슐화 시켜 같은 문제를 다른 알고리즘(기능)으로 해결 가능하게 한다.
* __구현법__
  + 기능별로 연관된 그룹을 모은다.
  + 그룹 내에서 기능을 캡슐화 한다.
  + 그룹의 내에서 캡슐화된 세부 기능이 서로 바꿀 수 있게 만든다.

#### __스트래티지 패턴은 어떤 경우에 적용될까?__
어떤 프로그램에 새로운 기능을 업데이트 해야하는데, 이 기능은 모든 클래스에 똑같이 들어간다고 가정해보자. 클래스를 배운 우리들은 아래와 같은 방법을 선택할 수 있을 것 이다.
1. 새로 업데이트된 기능을 모든 클래스들에 복사&붙여넣기
2. 새로운 기능을 가지는 클래스를 만들어 클래스를 다시 넣기

만약 (1)번을 선택한다면, 처음에는 편할 것이지만 여러번 기능을 수정/추가 한다면 번거롭고 유지보수가 불가능한 코드가 될 것이다.

(2)번을 선택한다면, 조금 더 스마트하게 작업할 수 있지만 패턴을 적용하면 더 깔끔해질 것이다.

#### __스트레티지 패턴을 구현해보자__
이걸보는 구독자는 오리 시뮬레이터를 개발하는 회사를 다니며 오리 시뮬레이터를 만들고 있는 개발자가 되보자.
* __초기 요구 사항__
  + 오리의 종류는 RedHeadDuck, MallardDuck, RubberDuck 세 종류가 필요.
  + 오리는 물에 떠있어야 한다.

자 단순히 생각해서 개발을 한다면, 아래와 같은 모양이 나올 것이다.

<details>
    <summary>예시 코드</summary>

{% highlight cpp linenos %}
#include <iostream>

class Duck {
public:
	void quack() { std::cout << "Quack Quack!!!" << std::endl; }
	void swim() { std::cout << "I can swimming!!!" << std::endl; }
	virtual void display() = 0;
};

class MallardDuck : public Duck {
public:
	void display() {
		std::cout << "= Mallard Duck =" << std::endl;
	}
};

class RedheadDuck : public Duck {
public:
	void display() {
		std::cout << "= Redhead Duck =" << std::endl;
	}
};

class RubberDuck : public Duck {
public:
	void display() {
		std::cout << "= Rubber Duck =" << std::endl;
	}
};
{% endhighlight %}

</details>

[FirstDevelop]

Duck이라는 부모 클래스를 만들고, 이것을 상속받아 자식 클래스로 각 종류별 오리들을 만들게 될 것이다.

개발이 끝나고 다음 회의에서 수정사항이 나왔다.
* __수정 사항 1__
  + 시뮬레이터의 현실성을 높이기 위해 날 수 있는 오리는 날 수 있어야 한다.

만약, 당신이 Duck 클래스(부모)에 Fly()라는 함수를 추가한다면, 당연히 문제가 생길 것 이다.

[ProblemModify]

<details>
    <summary>예시 코드</summary>

{% highlight cpp linenos %}
class Duck {
public:
	void quack() { std::cout << "Quack Quack!!!" << std::endl; }
	void swim() { std::cout << "I can swimming!!!" << std::endl; }
	void fly() { std::cout << "I can fly!!!" << std::endl; } // ADD FLY
	virtual void display() = 0;
};
{% endhighlight %}

</details>

RubberDuck(장난감 오리)가 나는 기이한 현상을 볼 수 있을 것이다. 그렇다면, 우리는 FlyAble클래스에 Fly()인터페이스를 만들고 이것을 오버라이드하여 각각의 오리들별로 기능 구현을 해주었다. 

[ProblemSecondModify.png]

<details>
    <summary>예시 코드</summary>

{% highlight cpp linenos %}
class Duck {
public:
	void quack() { std::cout << "Quack Quack!!!" << std::endl; }
	void swim() { std::cout << "I can swimming!!!" << std::endl; }
	virtual void display() = 0;
};

class FlyAble {
public:
	virtual void fly() = 0;
};

class MallardDuck : public Duck, public FlyAble {
public:
	void fly() {
		std::cout << "I can fly!!!" << std::endl;
	}
	void display() {
		std::cout << "= Mallard Duck =" << std::endl;
	}
};

class RedheadDuck : public Duck, public FlyAble {
public:
	void fly() {
		std::cout << "I can fly!!!" << std::endl;
	}
	void display() {
		std::cout << "= Redhead Duck =" << std::endl;
	}
};

class RubberDuck : public Duck {
public:
	void display() {
		std::cout << "= Rubber Duck =" << std::endl;
	}
};
{% endhighlight %}

</details>

아직까지는 기능하나를 복사/붙여넣기 하는 것은 그렇게 큰 문제는 되지 않으니 상관 없을 것이라고 생각했다.

드디어 프로그램을 런칭하였다. 회사는 앞으로 사용자들의 요구 사항을 반영하여 기능을 업데이트 하고, 새로운 오리 종류도 계속하여 추가한다고 발표하였다. 첫 번째 사용자들의 요구 사항이 생겼고 회사는 그것을 추가하기로 결하였다.
* __수정 사항 2__
  + 각 오리별로 소리를 낼 수 있어야 한다.

일단 똑같이 quack()이라는 함수를 만들어 fly()와 같이 오버라이드 해서 구현하였다.

[ProblemThirdModify]

<details>
    <summary>예시 코드</summary>

{% highlight cpp linenos %}
#include <iostream>

class Duck {
public:
	void swim() { std::cout << "I can swimming!!!" << std::endl; }
	virtual void display() = 0;
};

class FlyAble {
public:
	virtual void fly() = 0;
};

class QuackAble {
public:
	virtual void quack() = 0;
};

class MallardDuck : public Duck, public FlyAble, public QuackAble {
public:
	void fly() {
		std::cout << "I can fly!!!" << std::endl;
	}
	void quack() {
		std::cout << "Quack Quack!!!" << std::endl;
	}
	void display() {
		std::cout << "= Mallard Duck =" << std::endl;
	}
};

class RedheadDuck : public Duck, public FlyAble, public QuackAble {
public:
	void fly() {
		std::cout << "I can fly!!!" << std::endl;
	}
	void quack() {
		std::cout << "Quack Quack!!!" << std::endl;
	}
	void display() {
		std::cout << "= Redhead Duck =" << std::endl;
	}
};

class RubberDuck : public Duck, public QuackAble {
public:
	void quack() {
		std::cout << "Pick Pick!!!" << std::endl;
	}
	void display() {
		std::cout << "= Rubber Duck =" << std::endl;
	}
};
{% endhighlight %}

</details>

이쯤되면 의문이 하나 생긴다. 나중에 프로그램 규모가 커져서 오리는 더 만들어야하는 상황이다. 각 클래스에 오버라이딩 되어있는 fly()와 quack()은 전부 복사/붙여넣기 해야하고, 기능은 더 추가될 것이다. 이는 엄청 귀찮고, 코드가 더러워질 수도 있을 것이다.

자 그럼 __스트래티지 패턴__ 을 활용하여 코드를 수정해보자. 먼저 디자인 원칙 2가지를 알아보자.

###### 디자인 원칙
1. 상속보다는 구성을 활용한다.
2. 구현이 아닌 인터페이스에 맞춰서 프로그래밍 한다.

클래스와 상속을 배울때 우리는 ```A is B```와 ```A has B```를 배우게 된다. 지금과 같은 개발 상황에서는 ```A has B```가 더 나을 수 있다. 자 그럼 한번 현재 개발한 것을 부모 클래스부터 하니씩 쪼개보자.
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

자 이제 이것을 토대로 구현해보자.

[StrategyPattern]

<details>
    <summary>예시 코드</summary>

{% highlight cpp linenos %}
class FlyBehavior {
public:
	virtual void fly() = 0;
};

class FlyWithWings : public FlyBehavior {
public:
	void fly() override { std::cout << "I can fly!!!" << std::endl; }
};

class FlyNoWay : public FlyBehavior {
public:
	void fly() override { std::cout << "I can not!!!" << std::endl; }
};

class QuackBehavior {
public:
	virtual void quack() = 0;
};

class Quack : public QuackBehavior {
public:
	void quack() { std::cout << "Quack Quack!!!" << std::endl; }
};

class Squeak : public QuackBehavior {
public:
	void quack() { std::cout << "Pick Pick!!!" << std::endl; }
};

class MuteQuack : public QuackBehavior {
public:
	void quack() { std::cout << "I can't quack!!!" << std::endl; }
};

class Duck {
	FlyBehavior *flyBehavior;
	QuackBehavior *quackBehavior;

	void releaseFlyBehavior() {
		if (flyBehavior != nullptr)
			delete flyBehavior;
	}

	void releaseQuackBehavior() {
		if (quackBehavior == nullptr)
			delete quackBehavior;
	}

public:
	Duck() {
		flyBehavior = nullptr;
		quackBehavior = nullptr;
	}
	~Duck() {
		delete flyBehavior;
		delete quackBehavior;
	}

	void swim() { std::cout << "I can swimming!!!" << std::endl; }
	void fly() { if (flyBehavior != nullptr) flyBehavior->fly(); }
	void quack() { if (quackBehavior != nullptr) quackBehavior->quack(); }
	virtual void display() = 0;

	void setFlyBehavior(FlyBehavior *flyFunc) {
		releaseFlyBehavior();
		if (flyFunc != nullptr)
			flyBehavior = flyFunc;
	}

	void setQuackBehavior(QuackBehavior *quackFunc) {
		releaseQuackBehavior();
		if (quackFunc != nullptr)
			quackBehavior = quackFunc;
	}
};

class MallardDuck : public Duck {
public:
	MallardDuck() {
		setQuackBehavior(new Quack);
		setFlyBehavior(new FlyWithWings);
	}

	void display() {
		std::cout << "= Mallard Duck =" << std::endl;
	}
};

class RedheadDuck : public Duck {
public:
	RedheadDuck() {
		setQuackBehavior(new Quack);
		setFlyBehavior(new FlyWithWings);
	}

	void display() {
		std::cout << "= Redhead Duck =" << std::endl;
	}
};

class RubberDuck : public Duck {
public:
	RubberDuck() {
		setQuackBehavior(new Squeak);
		setFlyBehavior(new FlyNoWay);
	}

	void display() {
		std::cout << "= Rubber Duck =" << std::endl;
	}
};
{% endhighlight %}

</details>

이제 우리는 복사/붙여넣기가 아니라 각 그룹에 기능을 구현해서 넣으면 된다. 전보다 훨씬 편해지지 않았는가? 이런식으로 두 클래스를 합치는 것을 구성(Composition)을 이용하는 것 이라고 하는데, 여기의 여러 클래스는 행동을 상속 받는 대신, 올바른 객체로 구성되므로써 행동을 부여받게된다.

#### __마지막__
내가 이해한 것을 바탕으로 마지막으로 한 가지 예를 들자면, 만약 어떤 게임에서 이동한다라는 행위는 같은데 복도에서는 걸을 수 밖에 없고, 바깥에서는 뛸 수밖에 없고... 이런 한 행동한다라는 그룹내에서 패턴을 바꿀 때 유용한 것 같다. 

스트래티지 패턴은 수정/추가를 해야하는 부분이 오버라이딩한 모든 부분을 수정하도록 만드는 경우에 추가하면 편하게 작업할 수 있다. 