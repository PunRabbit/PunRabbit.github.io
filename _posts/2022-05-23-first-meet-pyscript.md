---
date: 2022-05-23 21:04:00
layout: post
title: PyCon 2022에서 공개된 PyScript라는 친구
subtitle: PyScript 첫인상
description: 신기한 기술
image: http://drive.google.com/uc?export=view&id=1L02K2HTmyHSa04grU2ELgqLr1GfwbUL4
optimized_image: http://drive.google.com/uc?export=view&id=1L02K2HTmyHSa04grU2ELgqLr1GfwbUL4
category: python
tags:
  - python
  - code
author: PunRabbit
---


## Run Python in Your HTML

이번 2022 Pycon에서 Anaconda사가 기묘한 기술을 공개했다.

그 이름도 바로 PyScript. 이름만 들어도 감이 오겠지만, Python을 JavaScript처럼 사용할 수 있게 해주는 프레임워크라고 한다.

자세한 기술 설명은 공식홈페이지만한 것이 없으니, 아래 공식 홈페이지 링크로 축약하겠다!

[https://pyscript.net/](https://pyscript.net/)


## 기묘한 느낌의 첫인상

처음 이 프레임워크를 마주했을 때, 기분이 상당히 기묘했다.

아마 이 글을 읽는 여러분도 예시 코드를 보면 기분이 기묘해질 것이다.

```html

<html>
	...
	<py-script> print('Now you can!') </py-script>

<html>

```

당장 위의 예시 코드를 마크다운으로 쓰면서도, 백틱에 이걸 뭐라고 써야할지 순간 멈칫했다.

익숙한 HTML에서 낯선 Python의 향기가 난다.


## 개인적인 견해

PyScript의 등장은 긍정적으로 느껴진다.

참 놀랍게도, 최근에 프론트엔드는 왜 JavaScript(or TypeScript)라는 언어에 한정적일까 라는 생각을 많이 하고 있었는데, 그런 고민을 한지 채 한달도 되지 않아 이렇게 툭 하고 새로운걸 던져주니, 좀 더 깊이 생각해볼 수 있을만한 아이디어가 제공된 것 같아 즐겁다.

하지만 마냥 긍정적인 생각만 있는 것은 아니다. 의문점도 분명히 존재한다.

JavaScript와 Python의 공통점이라고 한다면, 타입 지정에서 자유롭다는 것이 아닐까 싶다.

그런데 JavaScript의 경우에는, 이러한 자유가 결국 하나의 이슈가 되어버리면서 TypeScript의 탄생을 불러왔다.

여기서 드는 의문이 바로, 앞으로 PyScript가 어떤 위치에 존재하게 될 것인가? 이다.

Python내에서도 TypeHint를 통한 MyPy같은 방법을 이용해 타입을 사전에 검사하는 방법들이 존재하기 때문에 JavaScript와는 조금 다른 포지션에 있을지도 모르지만...

TypeScript와 비교한다면 글쎄... 모호한 기분이 든다.

물론 아직 Alpha 단계이고, 실사용자가 극명하게 적을 뿐더러 잔존, 발견된 이슈가 넘쳐나기 때문에 지금은 아무것도 판단할 수 없다.

오히려 이렇게 모호한게 많으니 나같은 사람들이 자유롭게 평가도 해보는 것이 아닐까 싶기도 하고...


## 그래서 쓸 것인가?

시도는 해볼 것 같다.

하지만, 현재 하고있는 실무에 적용하기에는 당연하게도 시기상조이다.

Anaconda에서 내놓은 공식 샘플을 보면, 현재로서는 데이터 통계나 인공지능적인 측면에서 부수적인 노력 없이 HTML을 구성할 수 있게 해주는게 메리트처럼 보이므로, 서버 개발자인 나에게는 모호하고 또 모호하다!

그렇지만 새로운 테크는 언제나 환영이기 때문에 조만간 써봐야겠다.

시간이 난다면 샘플로 작성하고 여기에 또 공유해야지!






