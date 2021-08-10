---
layout: default
title: URI
parent: Network
---


Table of contents


- TOC
{:toc}


---

# URI

- Uniform Resource Identifier

- 인터넷에 있는 자원을 나타내는 유일한 주소이다.

- URI의 하위 개념으로 URL, URN이 있다.

# URL

- Uniform Resource Locator

- 네트워크 상에서 자원이 어디 있는지를 알려준다.

# URI와 URL의 차이

이 둘의 차이점은 URI는 식별을 하고 URL은 자원의 위치를 가르킨다는 것이다.

다음의 예시를 통해 구분해보자.

- http://www.example.com
    - [http://www.example.com](http://www.example.com) 는 식별자로서 URI 이면서 URL이다.
- http://www.example.com/index.html
    - index.html 은 자원의 위치를 가르키고 있어서 URL 이면서 URI이다.
- http://www.example.com/index
    - index 는 실제 자원이 존재하지 않기 때문에 URL은 아니다. 하지만 내부 서버에서 작업을 통해 자원을 가르키기 때문에 URI라 할 수 있다.