---
date: 2022-05-18 22:58:00
layout: post
title: 데이터베이스 칼럼 명에 문법 넣기
subtitle: 될리가 없는데 왜 했을까???
description: 바보실수
image: http://drive.google.com/uc?export=view&id=1_44npeS6NN8R5rYwncPmv3ERo5S00tN0
optimized_image: http://drive.google.com/uc?export=view&id=1_44npeS6NN8R5rYwncPmv3ERo5S00tN0
category: DB
tags:
  - DB
  - code
author: PunRabbit
---


## 정말 바보같은 실수

최근 신규 DB를 만들어 운영할 일이 있었는데, 운영 중 크리티컬한 이슈가 발생해서 기록해놓으려고 한다.

사실 기록이라기보다는 흑역사에 가까울 정도로 기본적인 실수였는데, 당시 상황을 재현하자면 다음과 같다.

```py

# 예시 테이블 명은 SmartPhone, 변경하려는 칼럼명은 Case다.

UPDATE SmartPhone SET Case = '우드케이스' WHERE Name = '아이폰10';

```

당연하게도 에러가 발생했다.

칼럼명이 무려 Case다. Case.

문법으로 사용하는 명칭을 칼럼명에 넣으니, 당연하게도 에러가 발생했다.

너무나도 당연한 문제였다. 이를 간과했다는게 놀랍다.

물론 이런 에러를 회피하는 방법들이 없는 것은 아닌 것으로 안다. (Lower Case, Upper Case, etc...)

하지만 개인적으로 이는 너무나도 손해라고 생각한다.

유지보수적인 측면에서도, 사내 코드 컨벤션이나 전달사항적인 측면에서도, 부수적인 비용이 소비된다.

요즘 사소한 부분에서 놓치는 것들이 많은데, 경각심을 가지고 조심 또 조심해야겠다.


출장이라는 핑계로 사소해진 7번째 개발노트 끝!



