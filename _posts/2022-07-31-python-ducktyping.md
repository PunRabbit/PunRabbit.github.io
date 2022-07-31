---
date: 2022-07-31 01:42:15
layout: post
title: Python DuckTyping 확장해서 활용해보기 
subtitle: 유사 인터페이스
description: 유사제품주의
image: http://drive.google.com/uc?export=view&id=15Qe6135kmwP6iv72L1GstvzrvGGCsWTB
optimized_image: http://drive.google.com/uc?export=view&id=15Qe6135kmwP6iv72L1GstvzrvGGCsWTB
category: python
tags:
  - python
  - code
author: PunRabbit
---

## Python DuckTyping

파이썬에는 동적 타이핑의 종류 중 한가지인 덕타이핑이라는 개념이 존재합니다.

위키피디아의 깔끔한 설명을 빌려오자면 다음과 같습니다.

> 객체의 변수 및 메소드의 집합이 객체의 타입을 결정하는 것을 말한다.

<br/>


그리고 이걸 조금 더 쉽게, 제 언어대로 적어보자면...

> 객체에 포함된 메소드명이 동일할 경우, 해당 메소드를 호출하는 과정에서 그 어떤 객체를 할당해도 유연하게 작동한다.

<br/>

간단하게 샘플코드를 작성해보면, 아래와 같아요.

```python

#object는 python2 버전과의 호환을 위해 작성합니다.

class Dog(object):
	def __init__(self):
		self.name = "바보개"
		self.sound = "멍멍"

	def barking(self):
		return self.sound
	
	def running(self):
		pass
		

class Duck(object):
	def __init__(self):
		self.name = "바보오리"
		self.sound = "꽥꽥"
		
	def barking(self):
		return self.sound
		
	def flying(self):
		pass


def make_it_barking(it):
	print(it.barking())
		
```

위 코드를 보시면, Dog 객체와 Duck 객체가 동일하게 **barking** 이라는 메소드를 구현하고 있음을 알 수 있습니다.

그리고, **make_it_barking** 이라는 메소드를 통해 특정 객체를 인자로 받고 해당 객체의 barking 이라는 메소드의 결과값을 print하도록 구현했습니다.

<br/>

이제 이것들을 가지고 DuckTyping을 보여드릴게요.

```python

stupid_dog = Dog()
stupid_duck = Duck()

make_it_barking(stupid_dog)
make_it_barking(stupid_duck)

# "멍멍"
# "꽥꽥"

```

짜잔! 에러 없이 실행됩니다.

<br/>


위 코드의 결과값을 보시면 알 수 있듯이, python은 동적 타이핑을 지원하기 때문에, 메소드의 인자값의 타입을 강제하지 않습니다.

어떤 인자든 일단 실제로 받아서 구현부까지 가게되는거지요.

그렇기 때문에, 이 때 들어온 객체가 실제로 barking 이라는 메소드를 구현하고 있었다면, 별다른 에러 없이 해당 코드가 실행되는겁니다.

**이게 DuckTyping 입니다!**

빈약한 설명이어서 좀 부끄럽네요. 하하.

<br/>


## 우리는 여기서 하나 더 나아가보려고 합니다.

덕타이핑에 대해 간단하게 알아보았으니, 이참에 여기서 하나 더 나아가 봅시다!

Python은 타입을 강제하지 않지만, 사전에 **TypeHint**를 통해 타입을 정의할 수는 있습니다. 물론, 이렇게 정의하더라도 실제 동작하는데는 아무런 영향이 없습니다.

**하지만, 협업 측면에서는 분명히 유의미하죠.**

이 TypeHint를 통해 위에서 작성되었던 코드를 한번 개선해봅시다.

일단 위의 코드를 다시 가져와보겠습니다.

```python

class Dog(object):
	def __init__(self):
		self.name = "바보개"
		self.sound = "멍멍"

	def barking(self):
		return self.sound
	
	def running(self):
		pass
		

class Duck(object):
	def __init__(self):
		self.name = "바보오리"
		self.sound = "꽥꽥"
		
	def barking(self):
		return self.sound
		
	def flying(self):
		pass


def make_it_barking(it):
	print(it.barking())
		
```



**이 코드가 가진 문제점이 무엇일까요?**

상황을 하나 가정해봅시다!

비즈니스 로직을 짜는 과정에서, 동료가 위처럼 덕타이핑을 통해 구현된 로직을 공유했습니다.

여러분은 해당 로직을 받고, 한번 검토해보려고 합니다.

해당 로직 안에는, **barking**이라는 메소드가 구현된 객체를 받으면 될 듯한 **make_it_barking**이라는 메소드가 있었습니다.

**그런데, 이 barking이라는 메소드가 너무나도 모호합니다.**

Dog이라는 객체와 Duck이라는 객체는 분명 barking을 구현하고 있었고, 적절하게 덕타이핑으로서 활용된 듯 했습니다.

그런데 프로젝트를 살펴보니, 또 다른 Man이라는 객체가 barking이라는 메소드를 지니고 있었습니다.

여러분은 자연스럽게, Man 객체를 make_it_barking 메소드의 인자로 넘겨봅니다.

**이런, 에러가 납니다.**

Man 객체가 지닌 barking 이라는 메소드는 Dog, Duck이 구현하고 있는 barking 이라는 메소드와 이름만 같을 뿐, 전혀 다른 동작을 구현하고 있었기 때문이죠.

**바로 이게 문제입니다.**

인자로 받을 객체의 타입이 명시되지도, 강제되지도 않기 때문에 객체가 구현해야할 메소드의 세부적인 요구사항도 알 수가 없습니다.

알아내기 위해서는 구현부의 코드를 직접 하나하나 읽어보거나, DocString으로 작성된 내용을 검토해야합니다.

심지어 동일 메소드명이 겹치는 클래스들이 산재한다면, 검토해야 될 영역이 무지막지하게 늘어납니다.

**이는 협업에서 굉장한 패널티라고 생각합니다.**

그러므로, 우리는 이걸 개선해볼겁니다!

바로 샘플 코드로 넘어가보죠.




```python

# overrides는 별도로 설치해주셔야 합니다.
from abs import ABCMeta, abstractmethod
from overrides import overrides

class Animal(metaclass=ABCMeta):
	@abstractmethod
	def barking(self) -> str:
		pass


class Dog(Animal):
	def __init__(self):
		self.name: str = "바보개"
		self.sound: str = "멍멍"

	@overrides
	def barking(self) -> str:
		return self.sound
	
	def running(self) -> None:
		pass
		

class Duck(object):
	def __init__(self):
		self.name: str = "바보오리"
		self.sound: str = "꽥꽥"
		
	@overrides
	def barking(self) -> str:
		return self.sound
		
	def flying(self) -> None:
		pass


def make_it_barking(it: Animal) -> None:
	print(it.barking())


stupid_dog: Dog = Dog()
stupid_duck: Duck = Duck()

make_it_barking(stupid_dog)
make_it_barking(stupid_duck)

# "멍멍"
# "꽥꽥"
		
```

코드가 조금 복잡해졌네요.

추가된 사항들을 정리해드리자면 다음과 같습니다.

<br/>



1. **타입 힌트를 통한 모든 타입의 명시**

2. **클래스를 묶어주는 개념으로서의 Animal 추상클래스 생성**

3. **Animal에서 barking을 구현하도록 강제**

4. **Animal에 속하는 클래스들의 상속관계 재정의**

5. **make_it_barking 메소드에서 인자로 받을 객체의 타입을 Animal로 명시**

<br/>

위 5개를 통해 변경된 코드는 어떠한 장점이 있을까요?

**관계가 명확해졌습니다.**

위 코드로 다시 로직을 읽어보겠습니다.

make_it_barking이라는 메소드는 Animal 타입의 객체를 인자로 요구하고 있습니다.

그리고, Animal 타입을 지닌 객체들은 전부 string 값을 반환하는 barking 이라는 메소드의 구현이 강제되고 있습니다.

**이제 여러분이 검토해야 될 부분이 확 줄어들었습니다.**

이전처럼, 프로젝트 내 Man 이라는 객체가 barking 이라는 메소드를 구현하고 있더라도, 해당 객체가 Animal 타입이 아니라면 전혀 고려될 대상이 아닙니다.

**또한, 만일 여러분이 추가적인 객체를 생성하더라도, Animal 추상클래스를 상속하고, 해당 클래스가 강제하는 return 값이 정해진 메소드를 구현한다면, 동료가 넘겨준 로직을 확장성있게 이용할 수도 있습니다.**

어떤가요? 아까의 코드보다는 한결 나아지지 않았나요?

## 하지만 아직도 문제가 많습니다.

위의 개선사항도 아직 문제사항이 산재합니다.

언제나 그렇듯, 세상에 완벽한 코드는 없고 저는 그저 자그마한 주니어니까요.

위 코드가 가진 문제사항들에 대한 개선방안은 다음 글에서 찾아뵙도록 할게요 :>

제 글이 많은 사람들에게 도움이 되었기를 바라며!

<br/>


![image](http://drive.google.com/uc?export=view&id=1MO9bk9267h7BfSgY_CWNNyCYiYhX7mri)






