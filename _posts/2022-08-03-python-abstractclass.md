---
date: 2022-08-03 18:46:22
layout: post
title: Python Abstract Class 활용하기
subtitle: 추상화로 강제하기
description: 추상강제
image: http://drive.google.com/uc?export=view&id=1wt3Jj-2EQOlO-6EvzqFn3pypJnOHAQxj
optimized_image: http://drive.google.com/uc?export=view&id=1wt3Jj-2EQOlO-6EvzqFn3pypJnOHAQxj
category: python
tags:
  - python
  - code
author: PunRabbit
---

## Python AbstractClass

당연하게도, 파이썬에도 **추상클래스(abstract class)** 개념이 존재합니다.

이 추상클래스라는 친구는, 추상화된 메소드를 가짐과 동시에, 자신을 상속받는 클래스들에게 **추상메소드(abstract method)** 를 구현하도록 강제한다는 특징이 있습니다.

한마디로, 자신을 상속받는 모든 클래스들이 어떤 메소드를 구현하고 있는지 보여줍니다.

바로 예제로 보시죠!

```python

from abc import ABCMeta, abstractmethod

class Rat(metaclass=ABCMeta):
	def yelling(self):
		pass

	@abstractmethod
	def greeting(self):
		pass


class Jerry(Rat):
	def __init__(self):
		self.name: str = "귀여운 제리"

	def greeting(self):
		return "감사합니다."

```

위에 구현된 **Rat** 이라는 클래스가 추상클래스입니다. 추상클래스를 작성할 때는, 위 코드 처럼 **metaclass=ABCMeta** 를 작성해주셔야 합니다.

그리고 아래 **Jerry** 를 보시면, 추상클래스인 **Rat** 을 상속하고 있음을 알 수 있습니다.

그리고 **Jerry** 가 구현한 메소드를 보시면, 추상 클래스인 **Rat** 에서 요구한 **greeting** 이라는 메소드를 구현하고 있습니다.

여기서 한가지 의문이 듭니다.

추상클래스는 자신을 상속받는 클래스들에게 추상화된 메소드를 구현함을 강제한다고 했는데, Jerry 는 Rat 에서 구현된 **yelling** 이라는 메소드를 구현하고 있지 않습니다.

그럼 이 코드는 에러를 반환할까요?

아닙니다.

추상클래스와 마찬가지로, 추상메소드도 특별한 추가요소를 요구하는데요, 위 예제 코드에 있는 **@abstractmethod** 데코레이터가 바로 그 친구입니다.

yelling 메소드는 해당 데코레이터가 없어 구현이 강제되지 않지만, greeting 메소드는 abstractmethod 로 잡혀있기 때문에 구현이 강제됩니다.

따라서, 위 코드를 이렇게 바꾸면 인스턴스를 생성할 때 에러가 납니다.

```python

from abc import ABCMeta, abstractmethod

class Rat(metaclass=ABCMeta):
	@abstractmethod
	def yelling(self):
		pass

	def greeting(self):
		pass


class Jerry(Rat):
	def __init__(self):
		self.name: str = "귀여운 제리"

	def greeting(self):
		return "감사합니다."

```

<br/>

## Abstract Class 활용해보기

기본적인 기능은 알았으니, 우리는 이제 이걸 활용해보고자 합니다.

> 이전 글들을 미리 읽어보고 오시면 저의 의도를 이해하시는데 조금 더 도움이 될 것 같습니다! 
>> [Python DuckTyping 확장해서 활용해보기](https://punrabbit.github.io/python-ducktyping/)
>> [Python overrides 활용해보기](https://punrabbit.github.io/python-overrides/)
>> [Python slots 활용해보기](https://punrabbit.github.io/python-slots/)

<br/>

### **이번 핵심 아이디어는 다음과 같습니다.**

**추상클래스**는 자신을 상속받은 클래스들에게 **추상메소드**를 구현하는 것을 **강제**합니다.

추상클래스를 상속받아 구현된 클래스의 타입은 **추상클래스**로 정의될 수 있으며, 이는 대분류라고도 볼 수 있습니다.

Python의 **DuckTyping**은 객체의 타입을 제한하지 않고 동일한 메소드를 지녔을 경우 **유기적으로 해당 메소드를 실행**시켜줍니다.

Python에 있는 **TypeHint**를 이용하면 **각 객체의 타입을 명시**해줄 수 있습니다. 하지만, **타입을 검사하지는 않습니다.**

**overrides**와 **typehint**가 결합될 경우, 추상클래스에서 강제한 추상메소드가 반환할 타입의 **typehinting**이 제대로 되어 있는지 검사합니다. 하지만, **실제 타입을 검사하지는 않습니다.**

<br/>

감이 오시나요...?

사실 저도 막상 쓰고 읽어보니 감이 잘 안옵니다.

이번 핵심 아이디어를 한줄로 요약하자면 다음과 같습니다.

**추상클래스를 통해 강제한 추상메소드를 확장성 있게 활용하기**

예제 코드를 한번 적어보겠습니다.

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

코드를 하나하나 살펴볼게요.

일단 가장 상단에, 추상클래스인 **Rat** 과 **Cat** 이 있습니다.

그리고 각각의 추상클래스들은 str을 반환하는 **greeting** 과 bool을 반환하는 **running** 이라는 추상메소드를 가지고 있습니다.

Rat 을 상속하는 **Jerry** 와 **Ratatouille** 는 자신만의 방식으로 추상메소드를 **override** 하고 있습니다. 또한, 두 클래스 모두 단순히 추상메소드만을 구현하지 않고 **자신만의 메소드**도 하나씩 구현하고 있음을 알 수 있죠.

Cat 을 상속하는 **Tom** 도 다른 두 친구와 마찬가지로 추상메소드를 overrides 하고 자신만의 메소드인 **_say_name** 을 구현하고 있습니다.

아래 함수 **check_rat_name** 은 인자값으로 **Rat 타입**을 받을 것임을 **typehint** 를 통해 명시하고 있습니다.

새로운 동물 친구들인 **brown_animal**, **black_animal**, **gray_animal** 은 각 클래스들을 통해 생성된 인스턴스들입니다.

우리는 이제 check_rat_name 이라는 함수를 이용해보고자 합니다.

함수명에서 알 수 있듯이, 해당 함수는 rat 의 이름을 묻는 함수인 것 같습니다. 그리고 typehint 를 통해서도 인자값이 Rat 이라는 타입임을 알 수 있습니다.

**위와 같이 작성된 코드를 통해서라면, 우리는 간단하게 세 동물 친구들 중 누구누구의 이름을 알아낼 수 있는지 알 수 있습니다.**

> 좀 더 코드틱하게 이야기하자면, 어떤 인스턴스가 greeting 메소드를 가진지 알 수 있습니다.

check_rat_name 은 Rat 타입을 요구하고, brown_animal, black_animal 이 해당 Rat 타입이기 때문에, **우리는 해당 함수에 두 인스턴스를 넘겼을 때 아무런 오류 없이 정상적으로 반환될 것임을 예상할 수 있습니다.**

다르게 이야기하면, gray_animal 을 인자로 넘기게 되었을 경우에는 **에러가 발생하게 될 것이라고 예상할 수 있습니다.**


### **어떤가요? 코드가 좀 더 명확하지 않나요..!**

<br/>

그리고 위와 같은 코드는 또 하나의 장점을 지닙니다.

그건 바로, 여러분이 **추가적인 코드를 작성할 때** 입니다.

여러분이 만약, 제리나 라따뚜이 말고 **복슬이** 라는 Rat 타입의 클래스를 만들고 싶다고 가정하겠습니다.

여러분은 **자연스레** 추상클래스인 Rat 을 상속하고, 추상메소드를 구현한 클래스를 만들게 됩니다.

그리고 해당 복슬이 클래스를 통해, 기존에 Rat 타입의 인자를 요구하는 함수들을 사용하고 싶습니다.

여러분은 슬그머니 넣어봅니다.

**야호! 돌아갑니다!**

여러분은 그저 기존에 작성 되었던 추상클래스를 따라 만들었을 뿐인데, 기존에 사용하던 함수들이 그대로 작동됩니다!

### **이게 제가 생각하는 장점입니다.**

무언가 강제하는 것들이 많아져서 새로운 클래스를 작성하기가 더 어렵고 힘들어졌지만, 기존에 작성된 클래스들의 흐름(추상클래스)을 따라 자연스럽게 클래스를 작성했더니 **기존 로직에 있던 클래스를 쉽게 교체할 수 있습니다.**

> 물론, 실제 비즈니스 로직에서는 온갖 에러가 산재하지만요...

확장성 측면에서, 유의미한 코드 형태가 아닐까 싶습니다.


## 그럼 기존대로 작성하면 뭐가 문제인가요?

일단 **위 코드의 일부만을 가져온 뒤**, overrides와 typehint를 빼보겠습니다.

```python

from abc import ABCMeta, abstractmethod

class Rat(metaclass=ABCMeta):
	@abstractmethod
	def greeting(self):
		pass


class Cat(metaclass=ABCMeta):
	@abstractmethod
	def running(self):
		pass


class Jerry(Rat):
	def __init__(self):
		self.name = "폭풍인사 제리"

	def greeting(self):
		return f"{self.name} !!!!!"

	def _dance(self):
		print(f"춤추는 {self.name}")


class Tom(Cat):
	def __init__(self, name):
		self.name = name

	def running(self):
		if self.name == "톰":
			return True
		else:
			return False

	def _say_name(self):
		return self.name
	
	
# 여기서는 단순한 함수로 구현할게요.
def check_rat_name(who):
	return who.greeting()


brown_animal = Jerry()
gray_animal = Tom(name="톰")


print(check_rat_name(who=brown_animal)

# Error
print(check_rat_name(who=gray_animal)

```

**동작에는 아무런 차이가 없습니다.**

실제 저 두개를 뺀다고 해서 에러가 발생하지도 않습니다.

이제 코드를 다시 읽다보면, **굉장히 모호한 정보**들이 많아집니다.

check_rat_name 은 **who** 라는 인자를 받는데, who 는 **greeting 이라는 메소드를 가지고 있어야하네?**

아래 있는 Jerry 랑 Tom 클래스 중에 **어떤 클래스가 greeting 메소드를 구현하고 있을까?**

여러분은 자연스레 코드를 타고 다시 위로 올라가봅니다.

아, **Jerry** 클래스가 **greeting** 을 구현하고 있구나!

어떤가요? **봐야 하는 정보들의 범위가 넓어졌습니다.**

그리고, **일부 여러분들은 정보를 파악하는 순서도 뒤집혔을겁니다.**

이전 코드대로라면, 일부 여러분들은 check_rat_name 함수가 요구하는 인자인 ***who 의 타입이 Rat 인 것을 확인하고**, **해당 Rat 을 먼저 파악했을겁니다**. 그리고 해당 추상메소드를 상속받아 구현하는 구현체들을 확인하는 과정을 거쳤을겁니다.

### 이제 여기서 한가지 더 복잡한 이야기를 해보고자 합니다.

이전에 가정했던 **복슬이** 클래스 이야기를 다시 빌려오겠습니다.

여러분은 복잡한 비즈니스 로직 속에서, 복슬이라는 Rat 클래스를 새로이 만들고 싶었습니다.

여러분은 자연스레 추상클래스 Rat 을 상속받고, 추상메소드를 구현하여 복슬이를 만듭니다.

**그런데 여기서 한가지 복잡한 일이 생깁니다.**

Rat 의 추상메소드인 **greeting 이 무엇을 return 해야 하는지 알 수 없습니다.**

여러분은 둘 중 하나를 선택할 것입니다.

직감대로 string 값을 반환하도록 작성하거나, **다른 Rat 타입의 클래스를 찾아 greeting 메소드가 무엇을 return 해야 하는지 파악**할 것입니다.

추상클래스인 Rat 이 추상메소드의 구현을 강제하는데, **무엇을 만들어야 하는지 제대로 알려주지를 않는겁니다.**

여러분은 Rat 클래스를 추가하기 위해 다른 클래스들을 찾아볼 수 밖에 없었습니다.

아까 직면했던 상황과 똑같이, 봐야 하는 정보의 범위가 넓어진 것입니다.


## 그런데 여기서 추상클래스까지 빠져버린다면..?

아찔합니다. 바로 위 코드를 가져와 수정해보겠습니다.

```python

class Jerry:
	def __init__(self):
		self.name = "폭풍인사 제리"

	def greeting(self):
		return f"{self.name} !!!!!"

	def _dance(self):
		print(f"춤추는 {self.name}")


class Tom:
	def __init__(self, name):
		self.name = name

	def running(self):
		if self.name == "톰":
			return True
		else:
			return False

	def _say_name(self):
		return self.name
	
	
# 여기서는 단순한 함수로 구현할게요.
def check_rat_name(who):
	return who.greeting()


brown_animal = Jerry()
gray_animal = Tom(name="톰")


print(check_rat_name(who=brown_animal)

# Error
print(check_rat_name(who=gray_animal)

```

이제 여러분이 봐야 할 정보의 범위가 더 넓어졌습니다.

심지어, **그 넓은 정보들 속 무엇을 봐야할지 조차 어려워졌습니다.**

여러분은 이제 두가지 선택을 하게 됩니다.

**실제 check_rat_name 함수를 사용한 곳**을 찾아 거기에 들어간 인자값을 확인하고, **해당 인자값이 구성한 메소드**들을 확인하거나,

**greeting 이라는 메소드를 구현하고 있는 클래스들을 직접 찾아봅니다.**

그리고 당연하게도, 이제 **복슬이**를 만들기는 더더욱 어려워졌습니다.


## 강제에서 오는 명확함

**개발자는 항상 협업하고, 에러와 싸우는 존재라고 생각합니다.**

내가 쓴 코드를 남들이 깔끔하게 보고, 유용하게 이용하기 위해서는 남들이 **이해하고 쓰기 쉬워야 합니다.**

그리고, 내가 쓴 코드로 인해 발생하는 에러를 최소화 하기 위해서도 **남들이 이해하고 쓰기 쉬워야 합니다.**

그럼 이 이해하기 쉽고 쓰기 쉽게 하기 위해서는 **어떤 방법이 필요한가..?**

그저 작은 주니어일 뿐인 제가, 짧은 식견으로 생각해본 결과는... **강제해야한다** 입니다.

**이렇게 하면 안전해.** 라고 이야기하듯이 강제해야한다고 생각합니다.

물론, 이렇게 강제하는 과정에서 발생하는 부수적인 비용들도 만만치 않겠지만, 아직까지 저의 생각은 그렇습니다...!

**노련한 시니어 분께 저의 생각에 대해 여쭤보고 싶은 마음이 굴뚝같네요.**

<br/>

저는 이제 슬슬 인사하는 제리와 함께 사라집니다.

다음 포스팅은 아마, 예제 코드를 주의깊게 읽으신 분들은 무언가 눈치채셨을, overrides 된 메소드 이외의 메소드에 달려있던 **언더바( _ )에 대한 이야기**가 되지 않을까 싶습니다.

그럼 행복하세요! 


![myimage](http://drive.google.com/uc?export=view&id=1nUxYvlQ8Z7L2TLNUqJeANLe8rjmlLlzp)




 
