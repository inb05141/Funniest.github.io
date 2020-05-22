---
layout: post
title:  "데코레이터 패턴"
date:   2017-03-25 01:30:13 +0800
categories: Programming DesignPattern
tags: KR C++
---
> 이 포스팅은 __Head First Design Patterns__ 를 참고하여 작성하였습니다.  

## __데코레이터 패턴이란?__
* 객체에 동적으로 새로운 기능을 추가할 수 있는 ```디자인 패턴``` 이다.
  + 기능을 추가하려고 새로운 서브 클래스를 만드는 상황에서 사용할 수 있는 디자인 패턴이다.

## __데코레이터 패턴은 어떤 경우에 적용될까?__
이번에는 커피를 예제로 들어보면, 커피는 자바칩 프라푸치노, 아메리카노 등 많은 종류가 종류가 있다. 
스타벅스에서 커피를 시킨다고 가정하였을 때 우리는 바닐라 크림 프라프치노 + 자바칩 반갈기 + 휘핑 크림 + 자바칩 반 통으로... 이런식으로 더 많은 서브 데코레이트(?)를 추가한다.
기능도 마찬가지다. 어떠한 한 기능(여기서는 커피라는 클래스로 가정해보자)이 있을 때 추가적으로 시럽, 휘핑 등 여러 추가 기능을 추가해야하는 상황일 때 유용하게 쓰일 수 있다.

## __데코레이터 패턴을 구현해보자__
SI회사를 다니고있는데, 커피 음료를 만드는 포스 프로그램 의뢰가 들어 왔다.

* __초기 요구 사항__
  + 커피의 종류는 아메리카노, 프라푸치노 두 종류이다.
  + 데코레이터 종류로는 시럽, 휘핑 크림, 자바칩, 우유가 있다.
  + 각 커피에는 위와 같은 데코레이터를 추가할 수 있어야한다.
  + 각각의 상황에서 가격과 커피 이름이 잘 나와야 한다.

자 단순히 생각해서 개발을 한다면, 아래와 같은 모양이 나올 것이다.

![문제 발생](https://github.com/Funniest/Funniest.github.io/blob/master/_posts/Photos/DecoratorPattern/FirstDevelop.png?raw=true)

<details>
    <summary>예시 코드</summary>

{% highlight cpp linenos %}
#include <iostream>
#include <string>

class Beverage {
protected:
	std::string _desc;
public:
	virtual int getCost() = 0;
	std::string getDesc() {
		return _desc;
	}
};

class CaffeMocha : protected Beverage {
public:
	CaffeMocha() {
		_desc = "CaffeMocha";
	}

	int getCost() override {
		return 1000;
	}
};

class VanillaLatteCream : protected Beverage {
public:
	VanillaLatteCream() {
		_desc = "VanillaLatteCream";
	}

	int getCost() override {
		return 1000;
	}
};

class Americano : protected Beverage {
public:
	Americano() {
		_desc = "Americano";
	}

	int getCost() override {
		return 1000;
	}
};

class CaffeLatteWithShot : protected Beverage {
public:
	CaffeLatteWithShot() {
		_desc = "CaffeLatteWithShot";
	}

	int getCost() override {
		return 1000;
	}
};

class VanillaLatte : protected Beverage {
public:
	VanillaLatte() {
		_desc = "VanillaLatte";
	}

	int getCost() override {
		return 1000;
	}
};
{% endhighlight %}

</details>

이렇게 되면 되게 복잡해지지 않는가? 그럼 조금 개선해서 메인 클래스쪽으로 옵션을 옮겨보도록 하겠다.

![문제 발생](https://github.com/Funniest/Funniest.github.io/blob/master/_posts/Photos/DecoratorPattern/ProblemModify.png?raw=true)

<details>
    <summary>예시 코드</summary>

{% highlight cpp linenos %}
#include <iostream>
#include <string>

class Beverage {
	// options
	bool _milk;
	bool _shot;
	bool _cream;

protected:
	// Name
	std::string _desc;
public:
	virtual int getCost() = 0;
	std::string getDesc() {
		return _desc;
	}

	bool hasMilk() { return _milk; }
	void setMilk(bool milk) { _milk = milk; }
	bool hasShot() { return _shot; }
	void setShot(bool shot) { _shot = shot; }
	bool hasCream() { return _cream; }
	void setCream(bool cream) { _cream = cream; }
};

class CaffeMocha : protected Beverage {
public:
	CaffeMocha() {
		_desc = "CaffeMocha";
	}

	int getCost() override {
		return 1000;
	}
};

class Americano : protected Beverage {
public:
	Americano() {
		_desc = "Americano";
	}

	int getCost() override {
		return 1000;
	}
};

class VanillaLatte : protected Beverage {
public:
	VanillaLatte() {
		_desc = "VanillaLatte";
	}

	int getCost() override {
		return 1000;
	}
};
{% endhighlight %}

</details>

하지만 여전히 추가 옵션을 넣을 때 마다 코드 수정을 해야하고, 새로운 옵션이 추가되면 당연 새 함수를 추가해야한다. 위 보다 UML이 깔끔해 졌지만 유지보수에 문제가 있을 것 이다.

- - -

자 그럼 __데코레이터 패턴__ 을 활용하여 코드를 수정해보자.

### 디자인 원칙
1. OCP(Open close principle) 즉, 클래스는 확장에 대해서는 열려있어야 하지만, 코드 변경에 대해서는 닫혀 있어야한다.

* __부모 클래스 (Beverage)__
  + cost()
    - 가격을 정의하는 추상 클래스
  + getDesc()
    - 음료 이름을 가져오는 클래스

* __Drinks__
  + Americano, Latte, Mocha, etc...

* __CaffeDecorator With Beverage__
  + getDesc()
    - 순수 가상함수 클래스로 선언
  + cost()
    - 기존 가격 + 데코레이팅 가격

* __Decorators With CaffeDecorator__
  + Cream, Milk, etc...
  + getDesc()
    - 기존 이름 + 데코레이팅 이름
  + cost()
    - 기존 가격 + 데코레이팅 가격

자 이제 이것을 토대로 구현해보자.

![문제 발생](https://github.com/Funniest/Funniest.github.io/blob/master/_posts/Photos/DecoratorPattern/DecoratorPattern.png?raw=true)

<details>
    <summary>예시 코드</summary>

{% highlight cpp linenos %}
#include <iostream>
#include <string>

class Beverage {
protected:
	// Name
	std::string _desc;
public:
	virtual int getCost() = 0;
	virtual std::string getDesc() {
		return _desc;
	}
};

class Decorator : public Beverage {
public:
	virtual std::string getDesc() = 0;
};

class Milk : public Decorator {
	Beverage* _beverage;

public:
	Milk(Beverage* beverage) {
		_beverage = beverage;
		std::cout << "Add : " << beverage->getDesc() << std::endl;
	}

	int getCost() override {
		return _beverage->getCost() + 500;
	}

	std::string getDesc() override {
		std::cout << "Add : " << _beverage->getDesc() << std::endl;
		return ( "Milk " + _beverage->getDesc() );
	}
};

class Cream : public Decorator {
	Beverage* _beverage;

public:
	Cream(Beverage* beverage) {
		_beverage = beverage;
	}

	int getCost() {
		return _beverage->getCost() + 200;
	}

	std::string getDesc() {
		std::cout << "Hello" << std::endl;
		std::cout << "Add : " << _beverage->getDesc() << std::endl;
		return ( "Cream " + _beverage->getDesc() );
	}
};

class Shot : public Decorator {
	Beverage* _beverage;

public:
	Shot(Beverage* beverage) {
		_beverage = beverage;
	}

	int getCost() {
		return _beverage->getCost() + 400;
	}

	std::string getDesc() {
		std::cout << "Add : " << _beverage->getDesc() << std::endl;
		return ( "Shot " + _beverage->getDesc() );
	}
};

class CaffeMocha : public Beverage {
public:
	CaffeMocha() {
		_desc = "CaffeMocha";
	}

	int getCost() override {
		return 1000;
	}
};

class Americano : public Beverage {
public:
	Americano() {
		_desc = "Americano";
	}

	int getCost() override {
		return 1000;
	}
};

class VanillaLatte : public Beverage {
public:
	VanillaLatte() {
		_desc = "VanillaLatte";
	}

	int getCost() override {
		return 1000;
	}
};
{% endhighlight %}

</details>

만약 새로운 기능을 추가하고 싶으면 Decorator를 상속받는 추가 옵션을 만들면 된다. 만들어진 옵션은 new로 기존의 클래스에 더 하는 형태로 작업을 효율적으로 할 수 있게 된다.

## __마지막__
이렇듯 어떠한 객체의 타입과 호출가능한 함수를 그대로 유지하면서 그 객체에 새로운 기능을 추가하고 싶을 때나 상속을 통해 서브클래스를 계속 만드는 방법이 비효율적이라고 생각될 때 데코레이터 패턴을 사용한다.