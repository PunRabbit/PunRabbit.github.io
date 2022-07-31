
---
date: 2022-07-31 20:03:12
layout: post
title: Pypi overrides 활용해보기
subtitle: 강제의 미학
description: 활용의미학
image: http://drive.google.com/uc?export=view&id=1t5etlXxAN-r8RW4Uz4uSWiIEb91D8Q44
optimized_image: http://drive.google.com/uc?export=view&id=1t5etlXxAN-r8RW4Uz4uSWiIEb91D8Q44
category: python
tags:
  - python
  - code
author: PunRabbit
---

## Pypi overrides

Python 에는 Pypi 에서 만든 **overrides** 라는 라이브러리가 있다.

Java 에서 지원하는 override 와 거의 흡사한 기능을 지원하는데, 세세한 정보는 언제나 그렇듯 공식 홈페이지만한 것이 없기에 공식 홈페이지 링크를 아래와 같이 달아놓는다!

[https://pypi.org/project/overrides/](https://pypi.org/project/overrides/)

이 라이브러리에는 이런저런 기능들이 많지만, 일단 우리는 **데코레이터로 작동하는 overrides**에 집중하고자 한다.

<br/>



설치부터 들어가자!

```python

pip install overrides

```

간단하게 설치된다.

이제 이녀석을 활용해서 코드를 작성해보자.

<br/>


```python

from overrides import overrides
from abc import ABCMeta, abstractmethod

class Ball(metaclass=ABCMeta):
	@abstractmethod
	def bounce(self) -> bool:
		pass


class BasketBall(Ball):
	def __init__(self):
		self.type: str = "BasketBall"

	@overrides
	def bounce(self) -> bool:
		return True


class BaseBall(Ball):
	def __init__(self):
		self.type: str = "BaseBall"
		self.cost: int = 100

	@overrides
	def bounce(self) -> int:
		return self.cost


class StupidBall(Ball):
	def __init__(self):
		self.type: str = "StupidBall"

	def bounce(self) -> str:
		return "I'm Stupid!"

```

위 코드는 bounce 라는 추상메소드를 가진 Ball 이라는 추상클래스를 만들고, 해당 클래스를 상속하는 BasketBall, BaseBall, StupidBall 세 클래스를 작성한 코드이다.

그리고 여기엔 예시를 들기 위해 각 클래스별로 약간씩 다른 점들이 있다!

BasketBall, BaseBall 은 overrides 라이브러리를 사용해 오버라이딩한 추상메소드를 데코레이터로 잡아주었고, StupidBall 은 해당 라이브러리를 사용하지 않았다.

그리고 BasketBall 은 추상메소드가 기대하는 return 값을 잘 반환하고, 잘 명시하였지만 나머지 둘은 

이제 이걸 인스턴스로 올려보자!

```python

my_first_ball: Ball = BasketBall()
my_second_ball: Ball = BasketBall()
my_third_ball: Ball = StupidBall()

```

**이건 놀랍게도 에러를 뱉는다!**

과연 저 3개의 범인들 중 어떤 녀석이 에러를 뱉을까?

정답은 **my_second_ball** 이다.



## TypeHint 에 기반하여 오버라이딩을 검사하는 overrides

overrides 라이브러리의 첫 번째 강점은, 위의 예시에서 알 수 있듯이, 추상메소드에 적힌 typehint와 오버라이딩된 메소드에 적힌 typehint가 다를 때 에러를 발생시켜 준다는 것이다. 

**그런데 여기에도 치명적인 맹점이 있는데, 그것은 바로 typehint에 기반하기 때문에, 실제 타입 값을 검사하는 것이 아닌, hinting 된 타입을 검사한다는 것이다.**

즉, 위의 코드를 이렇게 바꾸면 에러가 발생하지 않는다.


```python

class BaseBall(Ball):
	def __init__(self):
		self.type: str = "BaseBall"
		self.cost: int = 100

	@overrides
	def bounce(self) -> str:
		return self.cost

```

실제 return 값의 타입은 string 이 아니지만, hint 된 타입에반 기반하기 때문에 overrides 가 정상적으로 된 것으로 인식하게 된다.

이는 **mypy**와 같은 추가적인 방법을 이용해 타입을 한번 더 정밀하게 검사하는 등의 방법으로 보완해야만 한다.

아쉬운 장점이 아닐 수 없다.

하지만, 우리에겐 두번째 장점이 있다.

## 추상클래스의 명확성 확보

이 내용을 설명하기에 적합한 제목인지는 모르겠다..!

간단하게 설명하자면, 추상클래스를 상속받은 클래스가 추상클래스의 형태를 명확하게 보여줄 수 있다는 것이다.

python에는 추상클래스를 상속받은 클래스가 가진 오버라이딩된 메소드를 명시할 방법이 따로 존재하지 않는다.

물론, 사내 컨벤션이나 암묵적인 규합을 통해 알아볼 수 있게 적을수는 있겠지만, **이는 언어 바깥에서 이루어지는 아웃라인의 룰이다.**

overrides는 이 아웃라인의 룰을 언어 안의 룰로 가져오게 해준다.

또한, 이렇게 명확하게 보여지게 될 경우, 코드 설계를 깔끔하게 잘 했다면(?), 추상클래스에 명시된 메소드들만 밖으로 노출하고 해당 메소드들만으로 잘 돌아가지 않을까 싶다.

예를 들자면 이런 느낌이다.

```python

# 샘플로 작성하기에 세세한 구현부는 제외했다.. 감안해주시기를!

from overrides import overrides
from abc import ABCMeta, abstractmethod


class Connector(metaclass=ABCMeta):
	@abstractmethod
	def connect(self) -> bool:
		pass


class GitConnector(Connector):
	def __init__(self):
		self.connection_name: str = "Git"
		self.connection_version: float = 2.0

	@overrides
	def connect(self) -> bool:
		self._bring_id()
		self._bring_pwd()
		return True
		
	def _bring_id(self) -> str:
		id: str = os.some_method()
		return id

	def _bring_pwd(self) -> str:
		pwd: str = os.some_method()
		return pwd
		

```

위의 코드를 보면, GitConnector 클래스는 Connector 클래스를 상속하고 있다.

그리고, Connector 클래스에 명시된 connect 이라는 메소드를 overrides 하며, 해당 메소드를 제외한 메소드들은 python에서 암묵적으로 사용하는 접근제한자인 _를 사용했다.

(**__를 사용하는 방법도 있으나,  맹글링은 사용 시 어떠한 이슈를 불러올 지 불명확하므로 섣부르게 사용해서는 안된다.**)

이는 즉, **overrides 해서 구현한 메소드만 외부로 노출시켜 해당 메소드만 사용하겠다** 는 의미이다.

그리고 이 의미는 이전에 포스팅했던 메모와 연결시켜 보았을 때, 훨씬 더 강력한 의미를 지닐 수 있다.

[Python DuckTyping 확장해서 활용해보기](https://punrabbit.github.io/python-ducktyping/)

<br/>




추상클래스를 상속받아 구현된 클래스들은 이제, 해당 추상클래스를 요구하는 그 어떤 곳에서도 유연하게 쓰일 수 있다.

또한, 해당 추상클래스에서 사용하는 메소드들만 밖으로 노출시켜 사용하므로, 클래스에 독자적으로 구현된 메소드들이 오용될 일도 줄어들었다.

(**물론, 강제되지 않아서 쓰려면 쓸 수 있는게 한계점이다.**)


## 강제의 미학

어찌 보면 overrides도 결국 하나의 강제성을 부여하는 라이브러리이다.

오버라이딩의 typehint를 강제하고, 강제한 메소드에 데코레이터를 달아 명시한다.

**이런 강제성들은 사용하기 불편하고, 설계 단계에서 더 많은 고민을 안겨주게 된다.**

**하지만, 이러한 강제성은 그 무엇보다 명확한 관계를 노출시킨다.**

개인적인 의견이지만, 다양한 협업이 이루어지는 회사에서 이러한 trade-off 상황은 마치 기울어진 시소같다.

>불편보다는 미학에 가깝지 않을까.

<br/>



오늘도 작은 주니어의 의견이 누군가에게 개미만큼의 도움이라도 되었기를 바라며...!

![myimage](http://drive.google.com/uc?export=view&id=1nUxYvlQ8Z7L2TLNUqJeANLe8rjmlLlzp)



