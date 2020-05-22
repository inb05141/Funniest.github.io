---
layout: post
title:  "옵저버 패턴"
date:   2020-05-22 09:08:00 +0800
categories: Programming DesignPattern
tags: KR C++
---
> 이 포스팅은 __Head First Design Patterns__ 를 참고하여 작성하였습니다.  

## __옵저버 패턴이란?__
* 한 객체에 이벤트가 발생(상태 변화)하였을 때 그 객체와 연관된 객체에게 상태 변화를 통보받고 자동으로 갱신될 수 있도록 하는 ```디자인 패턴``` 이다.
  + 게시/구독 모델로도 알려져있는 이 패턴은 상태가 변화할 객체와 이 객체를 관찰하는 관찰자(옵저버)들로 나뉘게된다. 상태가 변화하는 객체는 상태 변화가 있을 때마다 메서드 등을 통해 각 관찰자들에게 통지하도록하는 디자인 패턴이다.

## __옵저버 패턴은 어떤 경우에 적용될까?__
옵저버 패턴은 생각보다 많이 사용되고있다. 예를들어 스타벅스 앱을 설치하였다고 가정해보면, 당신은 처음 이벤트 정보 수신 알람 동의란에 체크를 하게 되었다. 스타벅스 앱은 새로운 이벤트가 발생할 때 마다 구독을 한 사용자에게 알림을 전달하게 된다.

위와 같은 경우 ```스타벅스 앱은 유저(Observer)에게 관찰당하는 객체```이고, ```이벤트 정보 수신 알람 동의란은 위 객체를 구독(Resigter)```하게 되는 부분이다. 만약 구독하게된다면 스타벅스 앱에서 이벤트가 업데이트 될 때 마다 ```모든 구독하는 객체는 알림(Notify)```을 받게된다.

이러한 예시는 유튜브, 뉴스 알림 등 다양한 곳에서 찾아볼 수 있다.

## __옵저버 패턴을 구현해보자__
> 구현에 앞서 옵저버 패턴은 PUSH/PULL 방식으로 나뉘어진다.  
>> PUSH 방식 : 관찰하고있는 객체의 내용이 변경될 때 마다 관찰자에게 알려주는 방식  
>> PULL 방식 : 관찰자가 필요할 때마다 관찰당하고 있는 객체에게 정보를 요청하는 방식

자 그럼 우리는 이제 기상청에 다니고 있는 개발자인데, 회사에 있는 날씨 장비의 데이터를 각각의 디스플레이에 출력해야하는 업무를 받았다.
* __초기 요구 사항__
  + 실제 기상정보를 수집하는 장비에서 온도, 습도, 압력 센서의 정보를 받는다.
  + 현재 기상 조건을 디스플레이에 출력해야한다.
  + 여러 디스플레이의 종류에 출력되어야 하며, 출력될 디스플레이 항목을 추가/제거 가능해야한다.
  + 시스템 확장이 용이해야한다.

자 단순히 생각해서 개발을 한다면, 아래와 같은 모양이 나올 것이다.

[그림]

이는 지금 각각의 디스플레이 마다 업데이트를 시켜주고 있기 때문에 시스템 확장에 대해 용이하지 않다.

- - -

자 그럼 __옵저버 패턴__ 을 활용하여 코드를 수정해보자.

### 디자인 원칙
1. 서로 상호작용을 하는 객체 사이에서는 가능하면 느슨하게 결합하는 디자인을 사용해야한다.

* __옵저버 클래스__
  + update
    - 기상정보가 변경되었을 때 옵저버(디스플레이)들 에게 전달되는 상태값

* __Each Displays With 옵저버 클래스__
  + update
    - 데이터를 화면에 표시하는 함수

* __서브젝트 클래스__
  + addObjeserver
    - 옵저버들을 등록하는 함수
  + delObserver
    - 등록된 옵저버들을 삭제하는 함수
  + notifyObserver
    - 모든 옵저버들에게 상태 변경을 알리기 위해 호출하는 함수

* __Weather Data With 서브젝트 클래스__
  + notifyObserver
    - 습도, 온도 등을 옵저버들에게 공지해주는 함수
  + delObserver
    - 구독중인 특정 옵저버 삭제
  + addObserver
    - 옵저버 등록

자 이제 이것을 토대로 구현해보자.

![문제 발생](https://github.com/Funniest/Funniest.github.io/blob/master/_posts/Photos/ObserverPattern/ObserverPattern.png?raw=true)

<details>
    <summary>예시 코드</summary>

{% highlight cpp linenos %}
#include <iostream>
#include <random>
#include <list>

class Observer {
public:
   virtual ~Observer() {};
   virtual void update(float temp, float humi) = 0;
};

class Subject {
public:
   virtual void notifyObserver() = 0;
   virtual void addObserver(Observer* observer) = 0;
   virtual void delObserver(Observer* observer) = 0;
};

class WeatherData : public Subject{
   std::list<Observer*> observerList;
   float temp;
   float humi;

public:
   WeatherData() { 
      temp = 0.0f;
      humi = 0.0f;
   }

   void updateWeatherData() {
      temp = 42.4f;
      humi = 65.0;
   }

   void notifyObserver() override {
      for (auto iter : observerList) {
         iter->update(temp, humi);
      }
   }

   void addObserver(Observer* observer) override {
      std::list<Observer*>::iterator index;
      index = find(observerList.begin(), observerList.end(), observer);

      if (observerList.end() != index)
         return;

      observerList.push_back(observer);
   }

   void delObserver(Observer* observer) override {
      std::list<Observer*>::iterator index;
      index = find(observerList.begin(), observerList.end(), observer);
      
      if (observerList.end() != index)
         observerList.erase(index);
   }
};

class Display01 : public Observer{
public:
   void update(float temp, float humi) override {
      std::cout << "DISPLAY 01" << std::endl;
      std::cout << "TEMP : " << temp << std::endl;
      std::cout << "HUMI : " << humi << std::endl;
   }
};

class Display02 : public Observer {
public:
   void update(float temp, float humi) override {
      std::cout << "[!] display 02" << std::endl;
      std::cout << "[+] temp : " << temp << std::endl;
      std::cout << "[+] humi : " << humi << std::endl;
   }
};
{% endhighlight %}

</details>

이제 옵저버 패턴의 객체들은 느슨하게 결합되어 상호 작용이 가능하지만, 서로에 대해서는 거의 알지못하는 상태이다. 각각의 옵저버들은 관찰하고 있는 것이 변하였을 때 서브젝트에게 통지를 받게된다.

## __마지막__
옵저버 패턴은 어떤 한 객체가 다른 객체들에게 정보를 갱신해야할 때 사용하면 좋다.