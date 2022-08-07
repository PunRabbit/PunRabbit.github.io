---
date: 2022-08-01 19:02:27
layout: post
title: Python &#95;&#95;slots&#95;&#95; 활용해보기
subtitle: 미리 정하고 강제하기
description: 예측하기
image: http://drive.google.com/uc?export=view&id=1dMYu95VPIOaitRB7WWm-3YxzdPrw1SZG
optimized_image: http://drive.google.com/uc?export=view&id=1dMYu95VPIOaitRB7WWm-3YxzdPrw1SZG
category: python
tags:
  - python
  - code
author: PunRabbit
---

## Python &#95;&#95;slots&#95;&#95;

Python 에는 **&#95;&#95;slots&#95;&#95;** 라는 재미있는 녀석이 있습니다.

양 옆 더블언더바를 보고 어느정도 예상하셨겠지만, 이 친구는 class 와 관련된 친구입니다.

일단 바로 샘플 코드부터 볼게요!

```python
# python2 버전과의 호환을 위해 object를 기입합니다!

class Bird(object):
	__slots__ = ["name", "length"]

	def __init__(self):
		self.name: str = "바보새"
		self.length: int = 100

```

항상 익숙하게 보던 &#95;&#95;init&#95;&#95; 위에 처음 보는 친구가 있을겁니다.

저 친구가 가진 기능은 뭘까요?

**&#95;&#95;slots&#95;&#95; 는 클래스 속성(attribute)을 사전에 제한합니다.**

Python은 기본적으로 클래스의 속성값이 **dict** 형태로 이루어져 있습니다. 이는, 인스턴스가 어느 곳에서든 새로운 속성값을 취하고, 버릴 수 있게 해주는 장점이 있습니다.

아래와 같은 코드가 실행될 수 있는 이유죠.

```python

class Bird(object):
	def __init__(self):
		self.name: str = "짱바보새"
		self.length: int = 100

fat_bird: Bird = Bird()
fat_bird.weight: int = 700

print(fat_bird.weight)

# 700

```

하지만, 이와 같은 특성은 이미 생성된 인스턴스의 속성값을 변경한다는 점에서, 어찌 보면 필요 이상의 램을 잡아먹게 됩니다.

그럼 사전에 속성값을 제한하는 &#95;&#95;slots&#95;&#95;를 사용하게 되면 어떨까요?

[https://wiki.python.org/moin/UsingSlots](https://wiki.python.org/moin/UsingSlots)

[https://book.pythontips.com/en/latest/__slots__magic.html](https://book.pythontips.com/en/latest/__slots__magic.html)

이는 위 두 링크에 나와있듯이, 클래스 속성이 더 이상 dict 형태를 취하지 않게 됨으로써 **허용된 속성 이외의 값을 받아들이지 않게 되고, 이렇게 고정된 속성값에만 용량을 할당하게 함으로써 메모리 사용량을 줄일 수 있게** 됩니다.

그래서 아래와 같은 코드가 더 이상 작동하지 않습니다.

```python

class Bird(object):
	__slots__ = ["name", "length"]

	def __init__(self):
		self.name: str = "짱바보새"
		self.length: int = 100

fat_bird: Bird = Bird()
fat_bird.weight: int = 700

# Attribute Error

```

상당히 괜찮아보이죠?

하지만, 한가지 의문이 들게 됩니다.

좋아보이는 친구인데, 다른 python 코드를 볼 때 저 친구를 본 적이 거의 없다싶이 합니다. **메모리 사용량을 줄여주는데, 왜 많은 사람들이 사용하지 않고 있을까요?**

## 일부 Python 라이브러리들, 메소드들은 &#95;&#95;dict&#95;&#95;를 필요로한다.

일단, 가장 큰 이슈는 호환성 이슈입니다.

간단한 하나의 예시를 들자면, **django에 있는 cached_property** 의 경우, class 의 &#95;&#95;dict&#95;&#95; 를 필요로 하기 때문에 &#95;&#95;slots&#95;&#95; 와 충돌합니다.

```python

class Man(object):  
    __slots__ = ["name"]  
  
    def __init__(self):  
        self.name: str = "Kim"  
  
  @cached_property  
  def call_name(self) -> str:  
        return self.name  
  
  
some_man: Man = Man()  
some_man.call_name()

# Attribute Error

```

이 경우에는 &#95;&#95;slots&#95;&#95;를 사용할 수 없을겁니다.

## 확장성이라는 장점을 버릴 만큼 유의미한가?

두 번째 이슈는, **속성이 &#95;&#95;dict&#95;&#95; 를 버림으로써 잃게 되는 확장성이라는 요소가 과연 &#95;&#95;slots&#95;&#95; 를 사용하는 것에 비해 유의미한가? 입니다.**

이 부분은 **trade_off** 적인 부분이라서, 사실 사용하기 나름이고, 판단하기 나름인 것 같습니다.

<br/>


그런데 저라면, 이 친구를 다른 의미로 차용할 것 같습니다.

## 강제함으로써 명확해지기

이전 글에서, 제가 생각하는 **강제의 미학**에 대해서 이야기했었는데요, 저는 그 강제함을 위해 이 녀석을 차용하는건 어떨까 싶습니다.

[Python overrides 활용해보기](https://punrabbit.github.io/python-overrides/)

설계자가 만든 의도대로 클래스를 활용하기 위해서는, 주어진 클래스의 속성값만 이용할 필요가 있다고 생각합니다.

제가 만든 강아지라는 클래스에는 분명 **무는 힘**이라는 속성이 없었는데, 제 클래스를 쓰는 다른 동료가 코드 상에서 특정 인스턴스에 임의적으로 무는 힘을 추가해서 **예상하지 못한 메소드** 또는 **불필요한 과한 인스턴스 속성** 만들어낼 수도 있으니까요.

그리고 그 특별한 속성을 지닌 인스턴스를 누군가가 다른 곳에 계속 이용하기 시작한다면, 작은 균열이 시작되는 것이라고 생각합니다.

**하지만 그렇다고 속성값을 제한하는 것이 마냥 옳다고는 볼 수 없습니다.**

Python을 사용하며 항상 고민할 수 밖에 없는, **Pythonic 한 코드**와 부합한지는 더 깊은 생각을 거쳐야만 합니다.

각 언어에는 제각기 특장점이 있으니, **언어에 구애받지 않는 개발자가 되더라도, 각 언어의 특징을 잘 활용해야만 한다고 생각합니다.**

하지만 저는 제 생각이 과연 옳은 것인지, Pythonic한 것인지 섣불리 판단할 수 없는 작은 주니어입니다 :>

얼른 더 넓은 시야의 사람이 되고 싶습니다.

오늘도 저의 작은 의견이 누군가에게 도움이 되었기를 바라며!

샘플 코드와 인사드립니다.

```python

from abc import ABCMeta, abstractmethod
from overrides import overrides

class Bird(metaclass=ABCMeta):
	@abstractmethod
	def flying(self) -> bool:
		pass


class Parrot(Bird):
	__slots__ = ["name", "weight"]

	def __init__(self, weight: int):
		self.name: str = "포도"
		self.weight: int = weight

	@overrides
	def flying(self) -> bool:
		if self.weight > 10:
			return True
		else:
			return False


class Penguin(Bird):
	__slots__ = ["name"]

	def __init__(self, name: str):
		self.name: str = name

	@overrides
	def flying(self) -> bool:
		return False


class BirdChecker(object):
	__slots__ = ["species"]

	def __init__(self, species: Bird):
		self.species: Bird = species

	def check_can_fly(self) -> str:
		if self.species.flying() is True:
			return f"{self.species.name} 는 날아!"
		else:
			return f"{self.species.name} 는 못날아!"

```

![myimage](http://drive.google.com/uc?export=view&id=1nUxYvlQ8Z7L2TLNUqJeANLe8rjmlLlzp)



