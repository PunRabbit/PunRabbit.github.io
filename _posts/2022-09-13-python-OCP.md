---
date: 2022-09-13 21:59:14
layout: post
title: 내가 이해한 SOLID - O
subtitle: Python으로 SOLID 살펴보기 3
description: 개방폐쇄
image: http://drive.google.com/uc?export=view&id=1C1b3VlAI8WvEvk3DPBYCTE-thmDJYM4a
optimized_image: http://drive.google.com/uc?export=view&id=1C1b3VlAI8WvEvk3DPBYCTE-thmDJYM4a
category: python
tags:
  - python
  - code
author: PunRabbit
---

## 내가 이해한 SOLID - O

> **!!경고!!** 이 시리즈는 제 주관적인 이해가 굉장히 많이 들어가 있습니다! 제가 틀린 이해를 하고 있을 가능성이 매우매우 높으니, 반드시 비판적인 시각으로 바라봐주세요 :>

> 이전 글을 읽고 오시면 더 좋습니다!

> [내가 이해한 SOLID - S and Why](https://punrabbit.github.io/python-solid-s-and-why/)

> [내가 이해한 SOLID - I](https://punrabbit.github.io/python-solid-i/)

**안녕하세요!**

오늘도 반갑게 돌아왔습니다 :>

오늘은 이전에 예고했던, SOLID의 **O**를 들고 왔어요!

개인적으로 **가장 흥미로운 부분**이 아닐까 싶은데요.

오늘도 여러분이 경고문을 필히 읽으셨기를 바라면서!! 들어가 보시죠!

## Open-Closed Principle

개방-폐쇄의 원칙.

한국어로 풀어 쓰자면 이런 말이 되지 않을까 싶습니다.

> **확장에는 열려있고, 수정에는 닫혀있어야 한다.**

이게 도대체 무슨 의미일까요...?

> 수정은 알겠지만 확장은 무엇을 의미하는걸까...?

아마 이 글을 읽고 계신 많은 분들이 **의구심을 가지실 부분**이 아닐까 싶습니다.

일단 먼저, 우리는 이걸 조금 더 **간단하게** 봐보고자 합니다.

어떻게 간단하게 보느냐!

문장을 이렇게 바꿔보겠습니다.

> **기능의 변경 또는 추가가 요구될 때 코드를 수정하나요 추가하나요?**

그렇습니다.

우리가 **항상 마주하는 일**이 있죠.

**기능을 변경**해야 하는 요구사항, **기능을 추가**해야 하는 요구사항.

우리가 항상 마주하는 이 일들이 모두 **확장**에 해당합니다.

그럼 이 확장에 해당하는 일들을 처리할 때, **여러분은 코드를 어떤식으로 변경**하나요?

기존의 **코드를 수정**하나요 아니면 **코드를 추가**하나요?

간단한 예시와 함께 더 자세히 알아보죠!

## 농협은행 전문 포인트 관리 서비스

우리는 **농협은행의 포인트를 전문적으로 관리해주는 서비스**를 운영하고 있습니다.

각 가맹점마다의 **포인트 적립율**과 **적립 상향선**을 세세히 관리하기 때문에 너무나도 유용한 서비스죠!

우리 서비스의 코드를 잠깐 보겠습니다.

```python

class CGV:
	def __init__(self):
		self.percent: float = 0.72
		self.limit: int = 5000

	def add_point(self, user: str, pay: int) -> None:
		# 간단하게 적을게요! 실제 로직 아닙니다!
		self._check_user_can_save(user=user)
		self._app_point_to_user(user=user, pay=pay)

	def _check_user_can_save(self, user: str) -> bool:
		self._check_user_limit(user=user)
		# Some Method about Check User can Save
		return can_user_save

	def _check_user_limit(self, user: str) -> int:
		# Some Method about Check Limit
		return user_rest_limit

	def _app_point_to_user(self, user: str, pay: int) -> int:
		# Some Method about Add Point to User
		return user_point


def cgv_add_point(user: str, pay: int) -> None:
	cgv: CGV = CGV()
	cgv.add_point(user=user, pay=pay)


cgv_add_point(user="A", pay=1000)

```

위 코드는 우리 서비스에서 관리하는 가맹점인 **CGV 클래스**입니다!

해당 클래스는 **농협은행에 철저히 맞춤 설계**된 인자들과 메소드들이 장착되어있죠!

우리는 아무런 문제 없이 서비스를 운영하고 있습니다.

**그런데 어느 날...**

## 우리 사업 확장한다! 신한은행도 추가해!

이런! 우리에게 **새로운 요구사항**이 떨어졌습니다!

서비스의 수익률이 좋으니, 사업을 확장해서 이제는 **신한은행의 포인트까지 관리**하고자 합니다.

여러분은 이제 신한은행 가맹점들의 **적립율**과 **적립 상한선**을 적용해서 **코드를 확장**해야합니다.

그런데... **앗 이런!**

**CGV는 농협은행의 가맹점이자 신한은행의 가맹점입니다!**

그럼 이제 우리는 위 코드를 어떻게 변경해야 좋을까요?

당장 떠오르는 것을 간단히 적어보자면...

```python

class CGV:
	def __init__(self):
		self.nong_percent: float = 0.72
		self.nong_limit: int = 5000
		self.shin_percent: float = 0.92
		self.shin_limit: int = 4000

	def nong_add_point(self, user: str, pay: int) -> None
		pass

	def shin_add_point(self, user: str, pay: int) -> None:
		pass


def nong_cgv_add_point(user: str, pay: int) -> None:
	cgv: CGV = CGV()
	cgv.nong_add_point(user=user, pay=pay)


def shin_cgv_add_point(user: str, pay: int) -> None
	cgv: CGV = CGV()
	cgv.shin_add_point(user=user, pay=pay)


nong_cgv_add_point(user="A", pay=1000)
shin_cgv_add_point(user="B", pay=2000)

```

이런 식으로 **CGV 클래스 내에 농협에 해당하는 정보들과 신한에 해당하는 정보들을 분리**해서 적어놓고, **필요할 때 적절한 메소드를 사용하는 것**이 떠오릅니다.

아니면, 로직 안에 **Flag를 세우고 if문으로 분기**하는 것도 떠오릅니다.

**여러분은 어떤가요?**

위 두 방법 중 하나를 떠올리셨나요? 아니면 다른 방법을 떠올리셨나요?

우리는 여기서, 우리가 떠올린 방법이 **개방폐쇄의 원칙에 적합한지 검토**해봐야 합니다.

먼저, 우리에게 떨어진 요구사항은 **기능의 확장**입니다.

확장에는 열려있는 것 처럼 보입니다.

그럼, 그 다음에 따라오는 **수정에는 닫혀있어야 한다는 원칙**에는 부합하는 것 같은가요?

조금 더 쉽게 보자면, 기능의 확장이 일어났을 때 여러분은 **코드를 수정했나요? 추가했나요?**

당장 위의 예시를 살펴보자면, **우리는 분명히 코드를 수정**했습니다.

기존에 있던 속성값을 변경했고, 클래스가 가지고 있던 메소드의 명칭도 변경했습니다.

코드의 추가가 있긴 했지만, **기존의 코드를 전면적으로 수정**하기도 했습니다.

확장에도, 수정에도 열려버렸습니다.

**이러면 어떤 문제가 발생할까요?**

일단, **해당 클래스가 사용되는 모든 곳을 찾아 바꿔야** 하는 일이 발생합니다.

**하나의 클래스에서 발생한 수정사항이 모든 비즈니스로직의 수정으로 이어지게 되는 것**입니다.

그런데 이 때, 우리는 앞서 살펴본 **S와 I에서 발견했던 가치**를 생각해봐야 합니다.

> 예상할 수 없는 영향을 배제하라

모든 비즈니스 로직을 수정하는 것은 **단순히 수고로운 것을 떠나, 어떤 버그를 마주하게 될지 모르게 됩니다.**

그럼 위와 같은 요청사항이 들어올 때 어떤 식으로 해결하는게 좋을까요?

저는 아래와 같은 방법을 제안합니다.

```python

from abc import ABCMeta, abstractmethod
from overrides import overrides


class CGV(metaclass=ABCMeta):
	@abstractmethod
	def add_point(self, user: str, pay: int) -> None:
		pass


class NongCGV(CGV):
	def __init__(self):
		self.percent: float = 0.72
		self.limit: int = 5000

	@overrides
	def add_point(self, user: str, pay: int) -> None:
		pass

	def _some_method_for_overrides(self) -> int:
		pass


nong_cgv: CGV = NongCGV()


def cgv_add_point(cgv: CGV, user: str, pay: int) -> None
	cgv.add_point(user=user, pay=pay)


cgv_add_point(cgv=nong_cgv, user="A", pay=1000)

```

**일단 농협만 존재할 때의 코드를 변경했습니다.**

우리는 **추상클래스**를 통해 CGV라는 클래스 타입이 어떤 메소드를 구현할지 명시해준 뒤, 해당 클래스를 상속하는 농협CGV를 생성했습니다.

그리고 실제 비즈니스 로직 쪽에서는 Python에 있는 **DuckTyping**을 통해서 CGV타입의 클래스를 확장성 있게 받을 수 있도록 해주었습니다.

**위 코드를 바탕으로 신한은행을 추가하면 아래와 같은 모습이 됩니다.**

```python

from abc import ABCMeta, abstractmethod
from overrides import overrides


class CGV(metaclass=ABCMeta):
	@abstractmethod
	def add_point(self, user: str, pay: int) -> None:
		pass


class NongCGV(CGV):
	def __init__(self):
		self.percent: float = 0.72
		self.limit: int = 5000

	@overrides
	def add_point(self, user: str, pay: int) -> None:
		pass

	def _some_method_for_overrides(self) -> int:
		pass


class ShinCGV(CGV):
	def __init__(self):
		self.percent: float = 0.92
		self.limit: int = 4000

	@overrides
	def add_point(self, user: str, pay: int) -> None:
		pass

	def _some_method_for_overrides(self) -> int:
		pass


nong_cgv: CGV = NongCGV()
shin_cgv: CGV = ShinCGV()


def cgv_add_point(cgv: CGV, user: str, pay: int) -> None
	cgv.add_point(user=user, pay=pay)


cgv_add_point(cgv=nong_cgv, user="A", pay=1000)
cgv_add_point(cgv=shin_cgv, user="B", pay=2000)

```

어떤가요?

코드의 수정이 일어났나요?

코드의 수정은 **극히 일부, 필요한 곳에서만 발생**했습니다.

> 인스턴스를 바꿔야 하는 부분과 같은 필연적인 부분에서는 수정이 일어날 수 밖에 없습니다. 물론, 이도 좀 더 럭셔리한 방법이 있지만! 이건 다음에 알아보도록 해요!

우리는 **신한은행을 추가한다는 요구사항에 대해 기존 코드의 수정 없이, CGV를 상속하는 신한CGV 클래스를 추가하는 것 만으로 요구사항을 충족**시켰습니다.

핵심적인 비즈니스 로직이나, 이미 사용되고 있는 핵심적인 클래스의 수정이 발생하지 않았기 때문에 **다른 코드들의 불필요한 수정도 발생하지 않았습니다.**

이는 곧 **우리가 예상하지 못하는 범위를 확 줄여주게** 됩니다.

개방폐쇄의 원칙에서 봤을 때도, 확장에는 열려있지만 수정에는 닫혀있는 구조에 근접하죠.

**확장이 일어나도 별다른 수정이 발생하지 않기 때문**이니까요!

## 우리는 수익을 내는 서비스를 개발하고 있기 때문에 핵심 클래스와 비즈니스 로직은 항상 보호받고 있어야만 한다.

우리의 서비스 안에 있는 핵심 클래스들과 비즈니스 로직들은 **항상 수정에 닫힌 상태로 보호**받고 있어야만 합니다.

그 이유는, 우리는 **수익을 발생시키는 회사의 서비스를 위한 서버를 개발하고 있기 때문**입니다.

**시장에서 서비스는 항상 변경됩니다.** 시장의 흐름을 따라가니까요.

그리고 서비스의 변경은 곧 **서버의 변경으로 직결**되게 됩니다.

즉, 우리의 **서버는 항상 변경에 노출**되어 있는 것입니다.

그런데, 이 수 많은 변경들 속에서, **우리가 예측할 수 없는 버그가 자꾸 터지고, 간단한 변경을 요구하는 요구사항에 대해서도 온갖 코드를 다 바꿔야해서 한나절이 걸리면 어떻게 될까요?**

수익이 떨어집니다.

서비스의 안정성이 떨어지고, 신뢰도가 떨어집니다.

**이런 일이 절대로 우리의 손에서 발생해서는 안되겠죠.**

**개방폐쇄의 원칙은 이러한 불상사를 막기 위한 하나의 장치**라고 보면 되지 않을까 싶습니다.

우리는 **수익을 창출하는 서비스를 개발하는 개발자**니까요!

<br><br/>

오늘은 우다다다다 쓰다보니 유독 주관적인 의견이 많이 들어가지 않았나 싶습니다!

언제나 그렇듯 자그마한 주니어의 의견이니, 제 의견에 전적으로 동의하기보다는 **하나씩 반박해보시는게 좋지 않을까** 싶습니다.

최근에 추석이었는데, 다들 즐거운 시간을 보내셨는지 모르겠네요!

그럼 저는 이만 스르륵 가보겠습니다.

**다들 행복하세요 :>**




![myimage](http://drive.google.com/uc?export=view&id=1277uFGv5qgWq_CRS9ggA0tHOBaq5CeDF)
