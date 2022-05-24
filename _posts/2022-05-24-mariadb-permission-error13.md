---
date: 2022-05-24 21:25:00
layout: post
title: MariaDB INTO OUTFILE Permission 에러 여행하기
subtitle: Permission Error 13 해결 방랑기
description: 에러해결
image: http://drive.google.com/uc?export=view&id=1chCs0qxYe21_uKHeG3e7fQMcHmRoc6Gs
optimized_image: http://drive.google.com/uc?export=view&id=1chCs0qxYe21_uKHeG3e7fQMcHmRoc6Gs
category: DB
tags:
  - DB
  - code
author: PunRabbit
---


## MariaDB Query 결과를 파일로 저장하다 생긴 일

오늘도 기묘한 에러를 겪었다.

가끔 생각하는건데, 에러 여행기를 하나의 책으로 엮으면 걸리버 여행기만큼 재밌지 않을까?

밈도 중간중간 삽화처럼 넣어주면, 꽤나 괜찮은 책이 나올지도 모른다.

오늘 넣은 밈도 직접 만든건데, 어쩌면 이거 진짜 될지도?

아니 중요한건 이게 아니라...

오늘 겪은 에러는 MariaDB의 쿼리문 결과를 파일오 저장하려다가 겪은 에러이다.

에러가 발생한 환경은 Windows 10 Pro, Mariadb 10.5 (installed at C drive) 이며,

에러가 발생한 쿼리문을 재현하자면 아래와 같다.

```sql

SELECT * FROM test INTO OUTFILE = "C:\pilot\test.txt"

```

입력과 동시에 아래와 같은 에러를 뱉었다.

```

ERROR 1 (HY000) : Can't create/write to file "C:\pilot\test.txt" (Errcode: 13 "Permission denied")

```

이게 뭐지? 싶어서 일단 CMD를 관리자 권한으로 실행해서 다시 진행해보았다.

어림도 없이 똑같은 에러를 뱉었다.

뭔가 다른 권한을 요구하는걸까? 싶어서 구글링을 진행해보았는데, root 계정에 file과 관련된 권한을 부여하라는 글을 발견했다.

root 계정에 추가적인 권한을 부여하라고..?

머리 속으로는 이해가 가지 않았지만, 일단 진행했다.

어림도 없이 똑같은 에러를 뱉었다.

잠시 고민해보다가, 혹시나 싶어 경로 구분자를 \에서 /로 변경해보았다.

```sql

SELECT * FROM test INTO OUTFILE = "C:/pilot/test.txt"

```

결과는 성공이었다.

근데 왜인지 모르겠지만 결과창에 성공한 경로가 다음과 같이 표시되었다.

```

"C:/pilottest.txt"

```

CMD 창에서 발생하는 오류겠거니 하고 해당 폴더를 진입했는데, 이런! 파일이 없다.

이해할 수 없지만 입력된 경로와 달랐겠거니 싶어서 표시된 C드라이브를 열어보았다.

이런! 여기에도 없다.

[image1](http://drive.google.com/uc?export=view&id=1S1ekXlIQnU5kYp56u7mWFGzvb_nk-w3S)

벌써부터 신나는 에러 여행을 할 생각에 가슴이 두근거렸다.

일단 성공했던 쿼리문을 다시 입력해보았다.

```

file already exist "C:/pilottest.txt"

```

세상에, 대체 어디에 저장했단 말인가?

바로 구글링을 진행해보았다. 그리고 한가지 힌트를 얻었다.

Linux 환경에서 일부 동일한 문제 (Permission Error)를 겪던 사람들이, chmod를 변경하고 sudo 권한을 줘도 죽어도 안해주던 MariaDB가 파일 경로를 /tmp 루트로 지정했더니 기적같이 저장했다는 것이다!

이거 이러면, 뭔가 기본으로 내재된 경로가 있을지도 모른다.

기본 경로...

추측 가는 곳은 한 곳이었다. my.ini가 있는 위치.

C드라이브 내에 Program Files/MariaDB 10.5/data 경로를 열어보았다.

드디어 찾았다. 망할놈.

대체 무엇이 원인이었는지는 모르겠으나, 그냥 자기 저장하고 싶은 곳에 저장한 것 같다.


## 근데 다른 환경에서는 또 잘 작동한다.

진짜 어메이징한 일이다.

Windows 10 pro로 같은 OS일 뿐만 아니라 DB가 설치된 위치조차도 같은 상황에서, MariaDB 버전만 10.5가 아닌 10.6으로 동일 쿼리문을 실행했는데..

정말 깔끔하게 저장된다. 심지어 원하는 경로에까지 넣어준다.

단순한 버전 차이일까..? 머리가 정말 띵하다. 좀 더 자세히 파고들어야하는데, 표본이 부족하다.

그리고, 10.6 환경에서는 10.5과 다르게 또 고유한 에러를 뱉는다.

상황은 쿼리문 OUTFILE 경로에 /가 아닌 \를 넣었을 때인데, 10.5에서는 Permission Denied (Errcode 13)을 뱉어주었으나, 10.6에서는 아래처럼 뱉는다.

```

ERROR 1 (HY000): Can't create/write to file "C:       pilotte     st.txt" (Errcode: 22 "Invalid argument")

```

[image2](http://drive.google.com/uc?export=view&id=1r4Z7YnW_2KlS0XVr2yMrbYD_UodxbwHK)

그런데 사실 DB는 죄가 없다.

경로에 무지성으로 \를 넣은 것 부터가 잘못된 판단이었으니...

아, 그러고보니 제대로 표시를 안했는데, CMD창에서 \를 입력할때는 \가 아니라 달러 표시로 보인다.

다들 이미 알고있겠지만, 혹시나 싶어서...

오늘 겪은 에러는 추후 더 자세히 파고들어서 정리해놓아야겠다. 단순히 버전 차이의 문제로 치부하기에는 기묘한 점이 너무나도 많으니!

