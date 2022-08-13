---
date: 2022-08-13 23:47:52
layout: post
title: Python Abstract Class 활용하기 2
subtitle: 아키텍처 추상화
description: 설계추상
image: http://drive.google.com/uc?export=view&id=1Q9ThpKG6EhvS-FvKidabD5CtL7EI764m
optimized_image: http://drive.google.com/uc?export=view&id=1Q9ThpKG6EhvS-FvKidabD5CtL7EI764m
category: python
tags:
  - python
  - code
author: PunRabbit
---

## Python Abstract Class 활용하기 2

안녕하세요 반가워요 :>

오늘은 **묘한 이야기**와 함께 찾아왔습니다.

이 이야기를 들으시기 전에, 이전에 적었던 이 글을 한번 보고 오시면 좋을 것 같아요.

> [Python Abstract Class 활용하기](https://punrabbit.github.io/python-abstractclass/)

Python으로 추상클래스를 작성하고 그 상속관계를 명확하게 한다면, 덕타이핑을 좀 더 **유연하고 단단하게** 사용할 수 있게 됩니다. **일종의 인터페이스같은 느낌**이지요.

이와 관련하여 제가 이전에 작성했던 예시를 다시 가져와볼게요.

```python

from abc import ABCMeta, abstractmethod
from overrides import overrides

class Rat(metaclass=ABCMeta):
	@abstractmethod
	def greeting(self) -> str:
		pass


class Cat(metaclass=ABCMeta):
	@abstractmethod
	def running(self) -> bool:
		pass


class Jerry(Rat):
	def __init__(self):
		self.name: str = "폭풍인사 제리"

	@overrides
	def greeting(self) -> str:
		return f"{self.name} !!!!!"

	def _dance(self) -> None:
		print(f"춤추는 {self.name}")


class Ratatouille(Rat):
	def __init__(self):
		self.name: str = "최고 요리사 라따뚜이"

	@overrides
	def greeting(self) -> str:
		return f"{self.name} ~!~!~~"

	def _cooking(self) -> None:
		print(f"요리하는 {self.name}")


class Tom(Cat):
	def __init__(self, name: str):
		self.name: str = name

	@overrides
	def running(self) -> bool:
		if self.name == "톰":
			return True
		else:
			return False

	def _say_name(self) -> str:
		return self.name
	
	
# 여기서는 단순한 함수로 구현할게요.
def check_rat_name(who: Rat) -> str:
	return who.greeting()


brown_animal: Rat = Jerry()
black_animal: Rat = Ratatouille()
gray_animal: Cat = Tom(name="톰")


print(check_rat_name(who=brown_animal)
print(check_rat_name(who=black_animal)

# Error
print(check_rat_name(who=gray_animal)

```

위의 예시를 간단하게 설명드리자면,

우리는 쥐의 이름을 확인하는 **check_rat_name** 메소드가 **인자**로서 실제 **Rat** 이라는 타입을 요구하고 있다는걸 알 수 있습니다.

그리고 우리는 Rat 이라는 **추상클래스**를 통해 어떤 메소드를 작성해야 하는지 알 수 있죠.

일종의 커다란 **설계도**가 있는 것입니다.

오늘의 이야기는 바로 **여기에서부터 출발**합니다 :>

## 추상클래스를 조금 더 크게 적용하기

지금까지의 예시들이나, 이야기들은 모두 어떤 작은 **기능**에 대하여 이야기 되어 왔다고 생각합니다.

요구사항의 변경에 따른 기능의 **변동을 유연하게 대처**하기 위해 작성하는 **설계도**가 **추상클래스**였습니다.

그런데 우리가 개발을 하다보면, 작은 기능의 변동 뿐만 아니라 **전체적인 로직의 코어한 부분이 확 바뀌는 상황**도 생깁니다.

특히나, 그 변동의 주체가 여러분이 작성한 코드나 내부적인 기능의 변동으로 인한 것이 아니라, **외부의 변동으로 인한 경우**, 꽤나 골치아픈 문제가 됩니다.

좋은 예시는 아니지만, **극단적이고 간단한 예시**를 적어볼게요.

여러분은 지금까지 **코드에 쿼리문을 직접 적는 방식**으로 SQL 데이터베이스에 접근해왔었습니다.

그런데, 이번에 기존에 사용하던 SQL 데이터베이스를 **MongoDB로 전면 교체**한다고 결정이 났습니다!

> 윽, 실제로 이렇게 급작스럽게 다 이관하는건 있어서는 안되요.

이제 여러분의 **머리에 불이 나기 시작**합니다.

로그인부터 게시판... **데이터베이스를 참조하는 온갖 코드들에 쿼리문들이 덕지덕지** 있었기 때문이죠!

> 여기서 많은 할말이 목끝까지 차오르시겠지만..! 극단적인 예니까 참아주시길!

> 다른 사소한 예시로는, 기존에 사용하던 http 관련 라이브러리가 **치명적인 보안 결함**이 발견되어 다른 라이브러리로 교체해야한다거나 하는 예시가 있지 않을까 싶어요 :>

이걸 하나하나 다 고치려면 **야근**만이 답일 것입니다.

하지만 우리는 이런 상황을 피해야만 하겠죠...?

먼저, 문제부터 파악해보겠습니다.

가장 큰 문제사항은, **외부와 연결된 라이브러리 또는 환경에 너무 종속적인 코드**가 짜여져 있다는 것입니다.

내부에 작성된 로직 안에 해당 외부 환경에 종속적인 메소드들이 산재해있으니 **외부 환경의 변화에 예상치 못한 치명적인 타격**을 받게 되는 것입니다.

그렇다면 이걸 어떻게 해결해야할까요?

저는 **여러분께 작은 아이디어를 제안**드리고자 합니다.

> 사실 좀 더 정확히는, 제가 스스로 떠올린 아이디어가 아니라, 아래 링크된 헥사고날 아키텍처 패턴을 보면서 이걸 어떻게 해볼까... 하다가 나온 작은 결론이에요. :>
> >[Hexagonal architecture](https://en.wikipedia.org/wiki/Hexagonal_architecture_(software))

그건 바로, 추상클래스를 더 크게 확장해서 적용하는 것입니다.

좀 더 자세히 이야기하자면, **내부에 구현된 로직과 바깥의 영역을 구분해서, 그 사이에 오가는 모든 길에 추상클래스라는 설계도를 놓는 방식**입니다.

위에 데이터베이스와 http의 예시에 대입해보면, 내부적으로 구현된 로직 바깥 영역에 있는 두 친구들에 대해서 추상클래스를 생성하고, **해당 친구들이 내부 로직으로 들어올 때는 반드시 추상클래스를 통해 구현된 덕타이핑을 거치게 하는 것**입니다.

이렇게 내부와 외부를 구분하여 그 사이에 추상클래스를 놓게 된다면, 이전에 언급했던 check_rat_name 이라는 메소드가 갖는 강점과 마찬가지로, **외부 환경의 변화가 발생하더라도 우리는 내부 로직의 변경을 최대한 피할 수 있습니다.**

조금 더 직관적으로 이야기하자면, **코드의 변경이 줄고 반대로 코드를 추가 작성하는 방향으로 바뀌는 것입니다.**

> 확장에는 열려있고 수정에는 닫혀있다...!?

어떤가요? 여러분이 생각하기에도 괜찮은 방법인 것 같은가요?

## 그럼 추상화하는게 너무 많아지는 것 아닌가요?

이에 대해서는 일단 **빈약하지만** 아래 글을 한번 읽어보시는 것도 나쁘지 않을 것 같습니다!

[Python Abstract Class로 어디까지 추상화 해야할까?](https://punrabbit.github.io/is-python-always-needs-abstractclass/)

그리고 사실, 이번에 제가 이야기드린 추상화에 대한 이야기는 **아키텍처적인 부분들과 더 맞닿아 있는 이야기**라서 제가 이 부분은 추후에 더 자세히 얘기해보고자 합니다 :>

마침 **회사에서 이런 일로 크게 고생**한 뒤로 클린 아키텍처를 열심히 읽고 있어서.. 다음에 올 때는 좀 더 **알차고 신뢰도 있는 글**로 찾아와보도록 하겠습니다!

<br/>


그럼 저는 오늘도 제리와 함께 사라질게요!

> 뭘 삼키는 그 모양대로 변하는 제리가 어쩌면 오늘의 이야기와 닿아있는..?...걸지도..?..모르죠!

**다들 행복하세요 :>**



![myimage](http://drive.google.com/uc?export=view&id=1277uFGv5qgWq_CRS9ggA0tHOBaq5CeDF)




 
