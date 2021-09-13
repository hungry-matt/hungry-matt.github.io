---
layout: default
title: No tests found for given includes
parent: Spring-Error
grand_parent: Error
---

Table of contents


- TOC
{:toc}


---


# No tests found for given includes

## 상황

신규 기능에 대한 검증을 위해 테스트 코드를 작성하고 테스트 실행중 오류가 발생하였다.

## 환경

- IntelliJ 

- Gradle 6

- Junit 5

## 문제

비슷한 경우의 오류를 이전에도 마주쳤지만 또 잊어버렸다...

문제는 빌드 테스트시 junit을 사용하기 위한 설정이 되어 있지 않아 아래와 같이 오류 로그가 출력되었다.

```
Execution failed for task ':test'.
> No tests found for given includes: [*****](filter.includeTestsMatching)

* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output. Run with --scan to get full insights.

```

## 해결

`build.gradle` 제일 하단에 아래 코드를 추가하면 정상적으로 작동하게 된다.

```
test {
    useJunitPlatform()
}
```


