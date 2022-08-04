---
date: 2022-08-04 20:15:37
layout: post
title: 기능을 추가해야 할 때 Python Abstract Class는 어떻게 변경해야 할까?
subtitle: 책임을 분리하자
description: 책임분리
image: http://drive.google.com/uc?export=view&id=1rxNDyMMPwBJ_VjxQSd61nbhrWN67o2-v
optimized_image: http://drive.google.com/uc?export=view&id=1rxNDyMMPwBJ_VjxQSd61nbhrWN67o2-v
category: python
tags:
  - python
  - code
author: PunRabbit
---

## 기능을 추가해야 할 때 Abstract Class 는 어떻게 변경해야 할까?

> **미리 스포일러 드립니다. 저도 아직 고민 단계에 머물고 있습니다. 지금까지 생각한 바를 정리할 뿐이니, 이번에는 정말 가볍게 봐주시면 감사하겠습니다 :>**

여러분은 문득 그런 의문이 들었습니다. 기존의 추상클래스 구조를 통해 작성된 코드가 있을 때, **갑작스러운 기능 추가가 요구될 경우** 과연 어떻게 대처해야 할까?

적당한 예시와 함께 이야기해보고자 합니다.

일단, 기준이 되는 샘플 코드부터 작성해보도록 하겠습니다.

```python

from abc import ABCMeta, abstractmethod
from overrides import overrides


class Bank(metaclass=ABCMeta):
	@abstractmethod
	def deposit(self, money: int) -> bool:
		pass

	@abstractmethod
	def withdraw(self, money: int) -> bool:
		pass


class ShinhanBank(Bank):
	def __init__(self, account: str):
		self.account: str = account
		self.bank_name: str = "신한은행"
		self.money: int = 100
	
	@overrides
	def deposit(self, money: int) -> bool:
		self.money += money
		return True

	@overrides
	def withdraw(self, money: int) -> bool:
		self.money -= money
		if abs(self.money) != self.money:
			return False
		else:
			return True


class WooriBank(Bank):
	def __init__(self, account: str):
		self.account: str = account
		self.bank_name: str = "우리은행"
		self.money: int = 300
	
	@overrides
	def deposit(self, money: int) -> bool:
		self.money += money
		return True

	@overrides
	def withdraw(self, money: int) -> bool:
		self.money -= money
		if abs(self.money) != self.money:
			return False
		else:
			return True


# 위와 같은 형태로 Bank 타입 클래스들이 3개 더 있다고 가정하겠습니다.
# 각 클래스 명은 IBKBank, NonghyupBank, KookminBank 입니다.

```

위 코드를 보시면, **Bank** 타입의 클래스가 총 5개가 있음을 알 수 있습니다.

각 은행 (클래스) 의 이름은 **신한은행**, **우리은행**, **기업은행**, **농협은행**, **국민은행** 입니다.

각 은행들은 출금과 입금 메소드를 구현함을 알 수 있습니다.

**그런데 이 때!**

**신한은행**과 **기업은행**에서 자체적인 포인트를 발급하고, **포인트 입출금**이라는 새로운 서비스를 제공한다고 합니다.

여러분은 이제 신한은행과 기업은행에 대해 포인트 입출금이라는 **새로운 메소드**를 만들어야 합니다.

> **과연 어떻게 만들어야 기존의 구조를 해치지 않고 유연하게 만들 수 있을까?**

## 추가적인 추상클래스를 만들고 상속관계를 교체하기

메소드를 추가하는 방식에는 굉장히 다양한 방식이 있습니다.

일단 첫번째로는, 기존의 **Bank** 추상클래스와 구분되는 **PointBank** 라는 추상클래스를 만들어 신한은행과 기업은행의 타입을 Bank 에서 PointBank 로 **교체**하는 방법입니다.

위와 같은 방법을 진행하게 되면, 아래와 같은 코드가 추가적으로 작성될 겁니다.

```python

from abc import ABCMeta, abstractmethod
from overrides import overrides


class Bank(metaclass=ABCMeta):
	@abstractmethod
	def deposit(self, money: int) -> bool:
		pass

	@abstractmethod
	def withdraw(self, money: int) -> bool:
		pass


class PointBank(metaclass=ABCMeta):
	@abstractmethod
	def deposit(self, money: int) -> bool:
		pass

	@abstractmethod
	def withdraw(self, money: int) -> bool:
		pass

	@abstractmethod
	def point_deposit(self, point: int) -> bool:
		pass

	@abstractmethod
	def point_withdraw(self, point: int) -> bool:
		pass

```

그리고 신한은행과 기업은행은 더 이상 **Bank 를 상속하지 않고 PointBank 를 상속하는 형태**로 변경됩니다.

나쁘지 않은 방법인 것 같습니다.

**기존 Bank 클래스에 변화를 주지 않으면서** 새로운 클래스를 생성해냈습니다.

이제 여러분은 변경된 타입의 신한은행과 기업은행 클래스를 **기존 비즈니스 로직에 붙입니다.**

**그런데 아뿔싸!**

여러분이 추가한 클래스 때문에 비즈니스 로직에 있던 메소드들 중, **인자 값으로 Bank 타입을 요구하던 메소드들의 typehint가 불명확**해졌습니다.

이제 여러분은 생각합니다.

기존 메소드들의 typehint를 변경하면 되지 않을까...?

하지만 동료가 만든 메소드에 추가적인 변화를 가하기 싫었던 여러분은 더 좋은 방법을 찾아보기로 합니다.

## 기존의 추상클래스에 abstract method 추가하기

두번째 방법은 **기존의 Bank 추상클래스에 포인트와 관련된 abstract method 를 추가**하는 방법입니다.

이 방법대로 한다면, 기존의 코드가 아래와 같이 변경될겁니다.

```python

from abc import ABCMeta, abstractmethod
from overrides import overrides


class Bank(metaclass=ABCMeta):
	@abstractmethod
	def deposit(self, money: int) -> bool:
		pass

	@abstractmethod
	def withdraw(self, money: int) -> bool:
		pass

	@abstractmethod
	def point_deposit(self, point: int) -> bool:
		pass

	@abstractmethod
	def point_withdraw(self, point: int) -> bool:
		pass

```

이렇게 한다면 **첫번째 방법에서 발생했던 문제**가 없어지게 됩니다.

기존의 상속관계를 변경할 필요가 없기 때문에, **비즈니스 로직에 있던 메소드들의 인자 값 타입을 변경할 필요가 없기 때문이죠.**

여러분은 이제 안심합니다.

**하지만 안심해서는 안됩니다.**

위와 같은 코드로 상속관계를 구현할 경우, **우리은행과 농협은행, 국민은행의 코드**는 어떻게 될까요?

간단히 우리은행만 구현하여 살펴보겠습니다.

```python

class WooriBank(Bank):
	def __init__(self, account: str):
		self.account: str = account
		self.bank_name: str = "우리은행"
		self.money: int = 300
	
	@overrides
	def deposit(self, money: int) -> bool:
		self.money += money
		return True

	@overrides
	def withdraw(self, money: int) -> bool:
		self.money -= money
		if abs(self.money) != self.money:
			return False
		else:
			return True

	@overrides
	def point_deposit(self, point: int) -> bool:
		pass

	@overrides
	def point_withdraw(self, point: int) -> bool:
		pass

```

우리은행은 아직 포인트 서비스를 시작하지 않았기 때문에, 포인트와 관련된 추상메소드를 전부 빈 형태로 구현하게 됩니다.

**이 코드가 오해를 불러일으키기 쉬워졌습니다.**

지금 우리는 **단 5개**의 예제만 가지고 운영했기 때문에, 실제 구현되지 않은 메소드를 파악하는 것이 어렵지 않습니다.

하지만 실제로 **수 많은 클래스들이 산재해있다면**, 여러분은 각 클래스마다 어떤 메소드가 구현되어있고 어떤 메소드가 구현되어 있지 않은지 **파악하는 것이 불가능**해집니다.

**추상클래스가 구현을 강제한 메소드들은 가능한 실제로 구현해주어야 합니다.**

이는 제가 예전 글에서 언급했던, [ducktyping을 활용한 코드 작성](https://punrabbit.github.io/python-ducktyping/)에 있어 **암묵적인 약속**입니다.

이 약속을 위배한다면, **다음과 같은 상황**이 발생할 수도 있습니다.

<br/>


여러분이 변경한 Bank 추상클래스를 보고, **여러분의 동료가 포인트와 관련된 메소드를 새로이 작성**합니다.

간단하게 예제를 적는다면, 다음과 같습니다.

```python

# 간단히 함수로 구현하겠습니다.
def add_your_point_with_double(bank: Bank, point: int) -> str:
	if bank.point_deposit(point=point) is True:
		return "축하합니다! 두배 포인트가 적립되었습니다!"

```

이 메소드를 작성한 동료는, Bank 라는 추상클래스를 보고 해당 메소드를 작성했기 때문에, **에러가 발생할 것이라고 쉽게 예상하지 못합니다.**

동료는 해당 메소드를 비즈니스 로직에 올립니다.

그런데, **일부 객체들에서 예상치 못한 결과가 반환**됩니다.

이상함을 느낀 동료는 해당 결과에 대한 로그를 확인하였고, 이내 일부 클래스들이 **추상메소드를 빈 형태로 구현하고 있음을 파악**했습니다.

> 어떤가요?

우리가 추상클래스에서 기대하는 **명확성**이라는 부분이 약해졌습니다.

물론, 위에서 언급한 문제 이외에 **SOLID와 관련한 문제**도 있지만, 해당 부분은 다음에 다뤄보겠습니다 :>

자, 이제 여러분은 다른 해결 방법을 찾아야합니다.

## 추가적인 추상클래스를 생성하고, 상속관계를 다중상속으로 변경하기

세번째 방법입니다.

첫번째 방법과 조금은 유사합니다.

여러분은 Bank 라는 추상클래스 이외에 **PointBank 라는 추상 클래스**를 생성합니다.

PointBank 에는 **포인트와 관련된 추상메소드만** 작성합니다.

그리고 여러분은 상속관계를 **교체하지 않고, 추가**합니다.

**신한은행**만 간단히 작성하자면 다음과 같습니다.

```python

from abc import ABCMeta, abstractmethod
from overrides import overrides


class Bank(metaclass=ABCMeta):
	@abstractmethod
	def deposit(self, money: int) -> bool:
		pass

	@abstractmethod
	def withdraw(self, money: int) -> bool:
		pass


class PointBank(metaclass=ABCMeta):
	@abstractmethod
	def point_deposit(self, point: int) -> bool:
		pass

	@abstractmethod
	def point_withdraw(self, point: int) -> bool:
		pass


class ShinhanBank(Bank, PointBank):
	def __init__(self, account: str):
		self.account: str = account
		self.bank_name: str = "신한은행"
		self.money: int = 100
		self.point: int = 100
	
	@overrides
	def deposit(self, money: int) -> bool:
		self.money += money
		return True

	@overrides
	def withdraw(self, money: int) -> bool:
		self.money -= money
		if abs(self.money) != self.money:
			return False
		else:
			return True

	@overrides
	def point_deposit(self, point: int) -> bool:
		self.point += point
		return True
		
	@overrides
	def point_withdraw(self, point: int) -> bool:
		self.point -= point
		if abs(self.point) != self.point:
			return False
		else:
			return True

```

이제 신한은행은 **Bank 와 PointBank 를 다중상속**하는 형태의 클래스가 되었습니다.

여러분은 생각합니다.

첫번째 방법에서 발생했던 **메소드 인자값 타입의 변경도 필요하지 않으며**

두번째 방법에서 발생했던 **빈 메소드 구현**도 일어나지 않습니다.

**이제 여러분의 동료는 포인트와 관련된 메소드를 아래와 같이 작성할 것입니다.**

```python

# 간단히 함수로 구현하겠습니다.
def add_your_point_with_double(point_bank: PointBank, point: int) -> str:
	if point_bank.point_deposit(point=point) is True:
		return "축하합니다! 두배 포인트가 적립되었습니다!"

```

이전보다 조금 더 명확한 코드가 되었습니다 :>

## 그래서 이렇게 해야되는게 맞나요?

> 조금 무책임할 수도 있지만...

서두에서 이야기했듯이, **이건 제 개인의 생각**일 뿐입니다.

심지어 저 조차도 조금 더 세련된 방법이 없을까 **고민하는 상황**이니까요.

분명 세상에는 더 좋은 방법들이 많을겁니다.

왜냐하면 저는 **행복한 세상의 자그마한 주니어**니까요 :>

이제 슬슬 인사하는 제리와 함께 물러나려고 합니다.

다음 글에서는 두번째 방법에서 이야기했던 **SOLID 와 연관하여 이번 이야기를 다시 재정리**하려고 합니다.

조금 더 깔끔한 이야기를 들고와보도록 할게요!

그럼, 오늘도 저의 자그마한 생각이 여러분에게 도움이 되었기를 바라며!


![myimage](http://drive.google.com/uc?export=view&id=1nUxYvlQ8Z7L2TLNUqJeANLe8rjmlLlzp)




 
