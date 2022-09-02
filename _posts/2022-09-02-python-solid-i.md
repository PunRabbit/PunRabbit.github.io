---
date: 2022-09-02 18:54:12
layout: post
title: 내가 이해한 SOLID - I
subtitle: Python으로 SOLID 살펴보기 2
description: 분리원칙
image: http://drive.google.com/uc?export=view&id=1gTlvLz_u_0qYw6-g_b2QFOv_oadLUf82
optimized_image: http://drive.google.com/uc?export=view&id=1gTlvLz_u_0qYw6-g_b2QFOv_oadLUf82
category: python
tags:
  - python
  - code
author: PunRabbit
---

## 내가 이해한 SOLID - I

> **!!경고!!** 이 시리즈는 제 주관적인 이해가 굉장히 많이 들어가 있습니다! 제가 틀린 이해를 하고 있을 가능성이 매우매우 높으니, 반드시 비판적인 시각으로 바라봐주세요 :>

> 이전 글을 읽고 오시면 더 좋습니다! - [내가 이해한 SOLID - S and Why](https://punrabbit.github.io/python-solid-s-and-why/)

**안녕하세요!**

다시 찾아왔습니다 :>

오늘은 이전 글에서 언급 했던 것 처럼, **인터페이스 분리 원칙**에 대해 이야기해보고자 합니다!

오늘도 위에 적어둔 경고문을 필히 읽으셨기를 바라면서! 바로 들어가 볼게요!

## Interface Segregation Principle

한국어로 직역하면 **인터페이스 분리 원칙** 정도입니다.

이 개념은 클라이언트가 자신이 이용하지 않는 메서드에 **의존**해서는 안된다는 의미를 지니고 있습니다. - Google Search

인터페이스를 분리하라는 원칙이라...

듣고 딱 감이 오는게 있으신가요?

아마 **Python**을 사용하시는 분들은 이게 어떤 의미인지 한번에 확 와닿기가 어려우실겁니다.

왜냐하면 **Python에는 인터페이스라는 기능적인 구현체를 기본적으로 제공해주지 않기 때문**입니다 :<

그럼 저희는 이것을 어떤 방향으로 이해해야 할까요?

저는 개인적으로 **Abstract Class**를 통해 이걸 이해하면 좋을 것 같다고 생각합니다 :>

> 이전에 비슷한 이야기를 적었어요! - [Python Abstract Class 활용하기](https://punrabbit.github.io/python-abstractclass/)

추상클래스를 통해 **빈 설계도를 만들고, 그 자식 클래스에게 세부적인 구현을 강제**함으로써 인터페이스와 유사한 형태를 구현할 수 있습니다.

물론 완벽히 같다고는 볼 수는 없기 때문에, **기본적인 이해의 영역에서만 염두**해두시면 될 것 같아요.

이전 글을 읽으셨다면 아마 **제일 마지막에 적혀져 있었던 예제 코드**를 기억하고 계실겁니다.

**물론, 새로 적어드립니다 :>**

```python

from abc import ABCMeta, abstractmethod  
from overrides import overrides


class SaveLoadModel(metaclass=ABCMeta):  
    @abstractmethod  
    def save(self) -> None:  
        pass  
  
	@abstractmethod  
    def load(self) -> None:  
        pass


class ImageSaveModule(SaveLoadModel):  
    def __init__(self):  
        self.file_path: str = "SomeWhere"  
  
    @overrides  
    def save(self) -> None:  
        # Some Method 
        pass  
  
    @overrides  
    def load(self) -> None:  
        # Empty Method 
        pass  
  
  
class ImageLoadModule(SaveLoadModel):  
    def __init__(self):  
        self.file_path: str = "SomeWhere"  
  
    @overrides  
    def save(self) -> None:  
        # Empty Method 
        pass  
  
    @overrides  
    def load(self) -> None:  
        # Some Method 
        pass


def image_save(save_model: SaveLoadModel):  
    save_model.save()  
  
  
def image_load(load_model: SaveLoadModel):  
    load_model.load()  
  
  
naver_image_save: SaveLoadModel = ImageSaveModule()  
naver_image_load: SaveLoadModel = ImageLoadModule()  

image_save(save_model=naver_image_save)  
image_load(load_model=naver_image_load)

```

위 코드는 이전 글에서 **단일 책임의 법칙을 설명하기 위해 적었던 예시 코드**입니다.

저장과 불러오기라는 메소드를 각각 **분리**함으로써 책임을 간단하게 나눠보는 예제였죠.

하지만 여러분도 이미 느끼고 계시다싶이, 위 코드는 **여전히 문제점**을 가지고 있습니다.

가장 눈에 들어오는 부분은 아마 **Empty Method** 라는 단어일겁니다.

비어있는 메소드. 즉, **pass로 이루어진 죽은 코드**입니다.

> 엥? 메소드에 왜 pass가 있어?

**추상클래스(인터페이스) 에서 구현을 강제했기 때문입니다.**

구현이 강제된 상태기 때문에 어쩔 수 없이 적어 넣은 것이죠.

그럼 이제 당연한 생각이 따라옵니다.

> 저걸 분리하면 되는거 아닌가?

맞습니다. 이제 이걸 분리해서 상속받으면 위와 같은 문제는 아래처럼 사라지게 될 것입니다.

```python

# 불러오는 구문은 생략하겠습니다.

class SaveModel(metaclass=ABCMeta):  
    @abstractmethod  
    def save(self) -> None:  
        pass  


class LoadModel(metaclass=ABCMeta):     
	@abstractmethod  
    def load(self) -> None:  
        pass


class ImageSaveModule(SaveModel):  
    def __init__(self):  
        self.file_path: str = "SomeWhere"  
  
    @overrides  
    def save(self) -> None:  
        # Some Method 
        pass  
 
 
class ImageLoadModule(LoadModel):  
    def __init__(self):  
        self.file_path: str = "SomeWhere"  
  
    @overrides  
    def load(self) -> None:  
        # Some Method 
        pass


def image_save(save_model: SaveModel):  
    save_model.save()  
  
  
def image_load(load_model: LoadModel):  
    load_model.load()  
  
  
naver_image_save: SaveModel = ImageSaveModule()  
naver_image_load: LoadModel = ImageLoadModule()  

image_save(save_model=naver_image_save)  
image_load(load_model=naver_image_load)

```

이제 죽은 코드도 없어지고, 보기에도 좀 더 깔끔해졌습니다!

> 진짜 쉽네!

아뇨. 안타깝게도, 이런 **간단한 예시이기 때문에 쉬워보이는 것**입니다.

약간 더 복잡한 예시를 들어보겠습니다!

여러분이 일하고 있는 회사는 지금 **입출금과 관련된 서비스**를 제공하고 있습니다.

그리고 해당 비즈니스 로직 안에는 **Bank라는 추상클래스가 있고, 해당 클래스는 입금과 출금이라는 메소드를 구현함을 강제**하고 있습니다.

해당 추상클래스를 상속받는 클래스들은 정말 많은데요, **대표적인 클래스로는 신한은행, 국민은행, 기업은행**이 있습니다.

여느 날과 다를 것 없이 일하고 있는 여러분께, **하나의 요청사항**이 날아옵니다.

> 농협은행이 우리 회사를 통해 서비스를 제공하기로 했다! 하지만 출금 서비스는 제공하지 않고 입금 서비스만 제공한다네.

자, 이제 여러분은 **심각한 고민의 늪**에 빠지게 됩니다.

분명 Bank 추상클래스를 받아 생성하면 될 것 같은데, **출금 서비스를 제공하지 않으니 해당 메소드는 어떻게 처리**해야하지?

선택의 기로입니다. **여러분은 어떤 선택을 하실건가요?**

만약 여러분이 가장 처음 작성되었던 예시처럼 **출금 서비스를 죽은 코드로 작성**한다면 굉장히 난처한 상황을 마주하게 될 것입니다.

왜냐하면 **여러분의 동료는 셀 수 없이 많고, 여러분이 모든 코드를 작성하지 않기 때문**입니다.

> 엥? 그게 무슨 의미인가요?

추상클래스(인터페이스)는 **일종의 약속**입니다.

**나 이런 메소드를 지니고 있으니, 너가 이 메소드를 요구한다면 나는 응당 대응해줄 수 있어.**

만일 여러분이 작성한 농협은행 클래스를 여러분이 아닌 **다른 누군가가 사용하게 되고, 그 누군가가 세세한 구현부를 살피지 않고 약속된 추상클래스대로 사용하려 한다면, 죽은 코드로 인해 분명한 에러가 발생**하게 됩니다.

> 그럼 어떻게 하라고?

추상클래스(인터페이스)를 **분리**하면 됩니다.

어디서 어디까지 분리할 것인지는 그때그때 내린 적합한 판단대로 분리하면 됩니다. **죽은 코드가 만들어지지 않게 하는 범위**에서요.

> 근데 그러면, 사용하지 않더라도 제대로 구현하면 괜찮은건가?

**물론 절대 아닙니다.**

사용하지 않는 메소드도 결국 **죽은 코드나 다름없고**, **불필요한 구현을 위해 많은 시간을 쏟을 필요도 없습니다.**

게다가 위에서 언급했던대로, **추상클래스(인터페이스)는 일종의 협약**인데, 사용하지 않는 메소드를 구현하며 해당 클래스를 상속받는다면 그건 **약속이 가지는 강제성과 명확성을 해치는 일이 됩니다.**

좀 더 나아가 보자면, 클라이언트가 불필요한 메소드에 의존하게 되면서 해당 클라이언트가 **받고, 주게 될 영향도 굉장히 불명확**해지는 일이기도 합니다.

제가 생각하는 SOLID의 지향점인, **불명확한 영향을 최소화한다는 이념과도 상충**되죠.

클래스는 항상 **자신이 실제로 사용할 메소드들만을 구현**하고 있어야 합니다.

그리고 그렇기 위해서는 **추상클래스(인터페이스)가 클래스가 실제 사용할 메소드들만을 강제**해야합니다.

즉, **인터페이스는 항상 최소한으로 분리가 되어있어야 한다**는거죠. 

그리고 그 최소의 기준이 **클라이언트**가 되는 거라 생각합니다.


## 하지만 성급한 추상화는 항상 조심해야 한다.

여기까지 쭉 읽으신 여러분은 이제 **생각이 많아지셨을 것이라 생각**합니다.

> 기능을 최소 단위로 쪼개서 추상클래스(인터페이스)를 생성해둬야하나..?

> 이제 앞으로 코드를 쓸 때는 미리 기능을 조각조각 분리해놓자!

> 아, 내가 적었던 코드들을 다 분리해야겠다!

**인터페이스 분리 원칙을 지키고자 기능을 세세하게 분리하는 것은 좋지만, 우리는 항상 성급한 추상화를 조심해야 합니다.**

예를 들어, **신발을 신는다.** 라는 행위를 보겠습니다.

여러분들은 이 신발을 신는 행위의 주체와 기능을 분리하고자 **왼발과 오른발을 각각 분리하여 추상화**시켰습니다.

그리고 신발을 신기 위해 이 **두 추상화된 클래스**를 받습니다.

기능을 최소 단위로 분리한 듯 보이지만, 아닙니다.

왜냐하면 **신발을 신기 위해서는 반드시 왼발과 오른발을 들어올리기 때문**입니다.

신발을 신는다는 행위를 구성함에 있어, **왼발과 오른발을 분리하여 생각할 필요가 없습니다.**

즉, **왼발을 든다 와 오른발을 든다 라는 기능은 반드시 붙어 작동하는 것**입니다. 이를 분리하는 것은 오히려 **더 복잡한 상속관계를 만들어낼 뿐**입니다.

> 좋은 예시가 아니지만 여러분이 잘 이해해주셨기를 바라며...!

**기능(인터페이스) 을 분리할 때는 반드시 명확한 기준을 잡고 분리**해야 함을 잊지 말아야 합니다.

**혹시 몰라** 라는 생각으로 모든걸 분리하고 추상화한다면 그건 오히려 **성급한 추상화로써, 코드를 더 복잡하게 만들게 될 뿐**입니다.

> 모든건 항상 적합하게 설계되고, 만들어져야 하니까요!

<br><br/>



이제 저는 주말을 즐기러 사라져야겠어요 :> 아마 다음 이야기는 **O**가 되지 않을까 싶네요!

오늘도 저의 자그마한 의견이 여러분께 도움이 되었기를 바라면서!

**행복하세용! :>**


![myimage](http://drive.google.com/uc?export=view&id=1aY64QvYamBaLQCM2vxjE3E_eFxmaWdGB)
