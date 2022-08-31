---
date: 2022-08-31 19:49:12
layout: post
title: 내가 이해한 SOLID - S and Why
subtitle: Python으로 SOLID 살펴보기 1
description: 단일책임
image: http://drive.google.com/uc?export=view&id=16SSSqOog6wxYhqbNLgsWe6H8B2cjxQJ0
optimized_image: http://drive.google.com/uc?export=view&id=16SSSqOog6wxYhqbNLgsWe6H8B2cjxQJ0
category: python
tags:
  - python
  - code
author: PunRabbit
---

## 내가 이해한 SOLID - S

> **!!경고!!** 이 시리즈는 제 주관적인 이해가 굉장히 많이 들어가 있습니다! 제가 틀린 이해를 하고 있을 가능성이 매우매우 높으니, 반드시 비판적인 시각으로 바라봐주세요 :>

**안녕하세요!**

오랜만에 다시 찾아왔습니다.

한동안 회사 일도 바쁘고, 휴가니 뭐니 하면서 나태하게 노느라 바빴어요...

이제 다시 고삐를 움켜쥐고 달릴 때가 되었습니다.

오늘은 이전부터 계속 언급해왔던 **SOLID** 개념에 대해 이야기 해볼까 해요.

물론, **각 개념들이 꽤나 무겁기 때문에**, 한 글에 하나의 개념을 설명하는 방식으로 진행할까 싶습니다!

본문으로 들어가기 전에, **혹시라도 위의 경고문을 읽지 않으신 분이 계시다면 필히 필독**해주세요!


## 무엇을 위한 개념 인가? Why?

이 시리즈 글의 서두에 **반드시 밝히고 싶은 이야기**라서 굵은 글씨로 적어봤습니다.

아마 이 글을 보고 계시는 여러분은 이미 **SOLID** 개념을 많이 들어오신 분들이실거라 생각합니다.

그리고 각자 이 개념에 대해 동의하기도 하고, 비판하기도 하면서 여러 생각을 거쳐 오셨을거에요.

그렇기에 더더욱, **이 개념이 무엇을 위한 개념인지 한번쯤 더 생각**해보고 가야한다고 생각합니다.

제가 생각하는 **SOLID**의 핵심은 **코드 수정(변경)으로 인해 발생하는 모든 예상치 못한 에러들을 사전에 방지**하는 것입니다.

각 객체들의 영역을 명확하게 그어주고, 해당 영역들이 어떠한 영역인지 명확하게 서술함으로써 **수정이 일어나더라도 그 영향이 예상 가능한 범위 내에서만 발생**하게 되는 것입니다.

그리고 이러한 요소들은 유연한 확장과도 이어질 수 있겠지요.

**SOLID의 5가지 개념들은 모두 하나의 공통된 목표를 가진 개념들임을 잊지 말아야 합니다.**

각 개념들이 제안하는 바와 제한하는 것들이 모두 다르지만, **5개의 개념들은 모두 하나의 공통된 목표를 위해 설계된 독립된 방안**들일 뿐이니까요.

> 그럼 이제 S로 들어가보시죠!


## Single Responsibility Principle

> S | 단일 책임의 원칙

**하나의 클래스(객체)가 하나의 책임만**을 가지는 것을 원칙으로 삼는다는 개념입니다.

여기서 하나의 책임이라는 것은 곧 하나의 액터, 액션 등 **외부에서 해당 객체를 사용하게 되는 주체**들과 직결되요.

클래스가 하나의 책임만을 가지고 있는지 판단하기 위해서는 해당 클래스에 대한 몇가지 판단을 해보아야 합니다.

1. **수정(변경)이 일어나는 원인이 하나인가?**

예를 들어, **A팀**의 요구사항이 변경되었을 때 수정이 일어나는 **Ticket** 이라는 클래스가 **B팀**의 요구사항 변경으로도 수정이 일어나고 있다면, **단일책임을 위배하고 있을 가능성이 높습니다.**

조금 더 세세하게 보자면, 만일 **Ticket** 이라는 클래스가 **결제**와 **정산**이라는 **두개의 비즈니스 로직**을 지니고 있다고 가정한다면, **정산팀**의 요청과 **결제팀**의 요청 **두개의 책임**을 모두 지니고 있게 되는 것입니다.

2. **하나의 클래스(객체)가 많은 비즈니스 로직을 수행하고 있지는 않은가?**

위에서 살펴본 예제는 **외부에서 들어오는 특정 요청에 의해 변경이 일어났을 때**를 가정하여 설명했습니다.

하지만 이번에 살펴보려고 하는 것은 외부의 요청에 의한 변경이 아닌, **단순히 하나의 객체가 너무 많은 비즈니스 로직을 수행하고 있지는 않은지**에 대한 것입니다.

예를 들어, 우리가 **특정 유저들에게 이메일을 전송하는 클래스**를 만들어야 한다고 가정하겠습니다.

이 때 필요한 기능들은 다음과 같습니다.

**특정 유저들을 선별, 수신 이메일 검증, 이메일 내용 검증, 메일 전송, 메일 전송 결과 로깅**

그렇게 우리는 **Email** 클래스를 만들고 위 기능들을 **모두 Email 클래스의 메소드**로서 구현했습니다.

짜잔! **다양하고 막중한 책임을 지닌 클래스**가 생성되었습니다.

이제 유저 선별 기준이 달라져서 코드를 변경할 때, **다른 기능들에 어떠한 영향이 생길지 파악**하기가 어려워졌습니다.

단일책임의 원칙의 핵심은 서론에 언급 했던 것 처럼, **변경에 의한 영향이 예측할 수 있는 범위 내에서만 발생하도록 한정**시키는 것이라고 생각합니다.

내가 바꾼 것으로 인해 다른 것들이 고장난다면, **그건 분명 이상한 일**이니까요.

예제 코드와 함께 조금 더 살펴보고자 합니다.

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
  
  
class ImageSaveLoadModule(SaveLoadModel):  
    def __init__(self):  
        self.file_path: str = "SomeWhere"  
  
    @overrides  
    def save(self) -> None:  
        # Some Method  
        pass  
  
	@overrides  
    def load(self) -> None:  
        # Some Method  
        pass  
  
	def _check_path(self) -> bool:  
        pass
  
  
def image_save(save_model: SaveLoadModel):  
    save_model.save()  
  
  
def image_load(load_model: SaveLoadModel):  
    load_model.load()  
  
  
naver_image_save_load: SaveLoadModel = ImageSaveLoadModule()  

image_save(save_model=naver_image_save_load)  
image_load(load_model=naver_image_save_load)

```

위 코드의 문제점은 무엇일까요?

아마 두개의 문제점을 파악하신 분들이 계실텐데, 일단 우리는 위에서 언급했던 하나의 문제점에 집중해서 보고자 합니다.

**ImageSaveLoaderModule** 이라는 클래스는 지금 **save**와 **load**라는 두개의 책임을 지니고 있습니다.

save를 필요로 하던 곳에서 변경 사항이 일어나도 해당 클래스를 수정해야하고, load를 필요로 하던 곳에서 변경 사항이 일어나도 해당 클래스가 수정되어야 합니다.

**그럼 이것을 어떻게 분리해야 할까요?**

```python

# 추상클래스 이후 부분부터 이어 적겠습니다.


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

자, 어떤가요?

**두 개의 책임을 분리하여 각 클래스를 생성하는 방법**으로 책임을 분리했습니다.

> 물론, 간단한 예시이기 때문에 쉽게 가능했습니다. 실제 돌아가는 비즈니스 로직에서는 더 세밀한 고민이 필요해요.

그럼 이제 위 코드는 단일책임에 대해서는 해방 되었다고 보여집니다.

**그런데 여러분도 느끼셨겠지만, 뭔가 찜찜한 것들이 남아있죠?**

이건 바로 다음에 이어질 글로 찾아뵙도록 하겠습니다 :>

> 살짝 스포해드리자면, SOLID의 I입니다.

<br><br/>

오늘도 저의 짧은 식견이 여러분께 도움이 되었기를 바라면서..!

**다들 행복하세요 :>!!**





![myimage](http://drive.google.com/uc?export=view&id=1aY64QvYamBaLQCM2vxjE3E_eFxmaWdGB)
