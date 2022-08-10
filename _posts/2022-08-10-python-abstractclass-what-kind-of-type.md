---
date: 2022-08-10 19:17:23
layout: post
title: Python Abstract Class를 상속받은 Class의 타입은 뭐야?
subtitle: 친구의 질문
description: 친구질문
image: http://drive.google.com/uc?export=view&id=1GuvYMJBui7iJYC9M5NTxNKtTRFg3UXXB
optimized_image: http://drive.google.com/uc?export=view&id=1GuvYMJBui7iJYC9M5NTxNKtTRFg3UXXB
category: python
tags:
  - python
  - code
author: PunRabbit
---

## Python Abstract Class를 상속받은 Class의 타입은 뭐야?

최근에 **친구**에게 받은 질문이었습니다.

> 제가 누굴 가르칠만큼 뛰어나진 않지만, 친구 한명이 **작은 관심**을 보여서 도와주고 있었거든요..!

바로 샘플 코드와 함께 살펴보겠습니다.

```python

from abc import ABCMeta, abstractmethod
from overrides import overrides


class Convertor(metaclass=ABCMeta):
	@abstractmethod
	def convert(self) -> None:
		pass

	@abstractmethod
	def check_possible_convert(self) -> bool:
		pass


class DaumConvertor(Convertor):
	# 세세한 구현부는 패스합니다!!
	def __init__(self):
		self.name: str = "다음"
		self.cost: int = 1000

	@overrides
	def convert(self) -> None:
		some_methods()

	@overrides
	def check_possible_convert(self) -> bool:
		some_methods()
		return True

	def _init_value_check(self) -> bool:
		return False
		
```

위와 같이 추상클래스와 그를 상속받는 클래스를 작성한 상태에서...

인스턴스를 올리는 코드를 작성하는 순간!

```pyhon

# 1
first_daum_convertor: Convertor = DaumConvertor()

# 2
second_daum_convertor: DaumConvertor = DaumConvertor()

```

친구의 의문점은 여기서 발생했습니다.

**DaumConvertor** 라는 클래스 인스턴스를 생성할 때, 해당 인스턴스의 **타입힌트**를 추상클래스인 **Convertor**로 적어야하는지, **DaumConvertor**로 적어야하는지 의문스럽다고 했습니다.

게다가 **mypy**로 둘 다 돌려봐도 **이상이 없다**고 나와서 더 혼란스럽다고도 했습니다.

> 둘 다 이상이 없다는데 그럼 내 맘대로 그냥 하면 되는거야?

> 음.. 아마도 아니.

저는 제가 할 수 있는 최선의 답변을 하기 위해 머리를 굴렸습니다.

## Convertor로 적는 것이 더 적합할 것 같아.

제가 친구에게 건넨 답변이었습니다.

> 둘 다 이상이 없는데 왜?

> 이상은 없겠지만 일단 사용성 측면에서는 DuckTyping에 적합하겠지.

일단, 우리는 지금까지 Abstract Class를 통해 DuckTyping을 **유연하게** 사용해보려고 해왔습니다.

> [Python DuckTyping 확장해서 활용해보기](https://punrabbit.github.io/python-ducktyping/)

만약 위에서 DaumConvertor의 타입을 DaumConvertor로 정의해버린다면, Convertor를 인자로 요구하는 메소드들에 **해당 인스턴스를 넘길 수 있는지 없는지 파악하기가 어려워** 질 것입니다.

예를 들자면 이런 이야기가 되지 않을까 싶습니다.

모든 **식당**에서는 음식을 팔기 때문에, 우리는 **네이버 지도**를 켜서 식당을 검색하면 우리가 일반적으로 생각하는 그 식당들을 확인할 수 있습니다.

그런데 새로 생긴 **따봉갈비집**이라는 곳은 **다른 식당들처럼 음식을 팔고 있지만**, 자신들은 식당이 아닌 따봉갈비집이라는 고유명사로 불리기를 원해 자신들의 정의를 **식당이 아닌 따봉갈비집**이라고 했습니다.

소문을 들은 여러분은 따봉갈비집을 찾기 위해 **네이버 지도를 켜고 식당을 검색**했지만, 따봉갈비집은 전혀 찾을 수가 없었죠.

적합한 예시가 되었는지 모르겠지만, 제가 이야기하고 싶은 바는 다음과 같습니다.

동일한 메소드를 지닌 객체들을 하나로 묶기 위해 대분류 개념의 추상클래스를 생성했는데, 그 하위 개념의 클래스를 타입으로 지정해버린다면 **묶은 의미가 없다.**

> 아~.. 알겠는데 뭔가 모르겠네

친구에게 열심히 설명해보았지만, 여전히 **무언가 의구심**이 드는 듯 했습니다.

좀 더 적합한 설명이 뭐가 있을까... 머리 속이 복잡해져왔습니다.

## 빵틀로 찍어낸 빵은 다 빵이잖아, 그럼 그 빵틀을 만든 빵틀제조기로 만든 빵틀도 다 빵틀이아닐까?

> 의도치 않았는데 읽기 힘든 라임이 생겼네요.

문득 친구가 Pyhon을 공부하면서 봤다던 class 설명 예시가 떠올랐습니다.

**class는 하나의 빵틀이고, 그 빵틀을 통해 찍어낸 빵들이 instance다.**

이를 이용해서 **조금 더 간단**하게 설명할 수 있지 않을까.. 라는 생각이 문득 들었습니다.

하지만 여전히 망설여졌던건, **완벽하게 맞는 설명이 아닌 것 같기 때문**이었습니다.

> 애매하게 모르겠다.

슬슬 피곤해보이는 친구를 보고, 저는 그냥 얘기하기로 마음먹었습니다.

> 빵틀로 찍어낸 빵은 다 빵이잖아, 그럼 그 빵틀을 만든 빵틀제조기로 찍어낸 빵틀도 다 빵틀이아닐까?

> 빵..빵틀..빵..빵..??

> Abstract Class를 특정 Class를 생성하기 위한 도면으로 쓰고 있다면, Class와 Instance 사이의 관계와 유사하게 생각해볼 수 있는게 있지 않을까?

**안타깝게도 친구의 표정은 더 미궁 속으로 빠져들어갔습니다.**

이야기는 해피엔딩이 아니네요.

결국 친구는 일단 알겠다는 말과 함께, 추후에 더 코드를 작성해보고 다시 물어보겠다고 했습니다.

**설명하는 능력이 얼마나 중요한지 뼈저리게 느껴지는 순간**이었습니다.

## Python TypeHint 를 적을 때는 한번 더 고민해보는 것이 좋은 것 같습니다.

> 이는 좀 곁다리인 이야기입니다!

저도 **초창기**에는 추상클래스를 상속받은 클래스의 타입을 뭐라고 정의하는게 좋을지 고민했었습니다.

> 아니 사실, 지금까지도 흔들리지 않을 확실한 근거를 찾기 위해 노력하고 있습니다.

당연히 둘 모두 틀린 것이 아니기에, 무언가 **확실한 정답**을 찾고자 머리를 싸맸었죠.

타입이 왜 중요한지도 정확히 모르겠는 시기에, **mypy**로 검사해도 틀리지 않고, 이론적으로 생각해도 딱히 틀리지 않은 것 같은 부분에 시간을 왜 쏟아야하나 생각했습니다.

그렇게 **mypy 기준으로 틀리지 않으면 타입을 그때그때 마음에 드는대로 막 적던 시기**가 있었습니다.

지금 와서 생각해보면, 저렇게 막 적을 수 있던 이유도, **추상클래스를 왜 써야하는지도 모르고 그냥 자바의 인터페이스를 따라해보고 싶다는 생각에 막 썼기 때문**이 아닌가 싶습니다.

시간이 흘러 DuckTyping에 대한 고민을 더 해보고, DI 개념을 접하면서 그나마 저만의 이유가 생겼습니다.

단순히 맞는 타입이 아니라, **적합한 타입**을 명시해야 **덕타이핑과 타입힌트의 장점**을 제대로 이용할 수 있고, 이후에 이야기할 **Uniitest**에서도 이렇게 깔끔하게 명시된 타입이 있어야 **덜 수고스럽게 테스트를 진행**할 수 있다... 라는 이유들입니다.

아직 이 이유들이 옳은지 틀린지는 모르지만... **언젠가 그 답을 멋지게 낼 수 있는 날**도 오겠죠?

<br/>

저는 그럼 이만 슬슬 돌아가도록 하겠습니다.

제리와 함께 편안한 밤 보내세요 :>




![myimage](http://drive.google.com/uc?export=view&id=1qIDt2bL9Sp1eqtKXRUcPaQSt3DzTzXBE)




 
