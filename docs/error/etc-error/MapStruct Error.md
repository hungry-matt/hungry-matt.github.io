---
layout: default
title: MapStruct 빌드 실패
parent: etc-error
grand_parent: Error
---

# 문제

MapStruct 사용 중 Lombok이 제대로 연동 되지 않아 빌드 시 오류가 발생했다.

source와 target에는 Lombok을 사용하여 Getter, Setter가 작성되어 있다.

```
Failed to read AstModifyingAnnotationProcessor
```

# 해결

Lombok 프로젝트에 올라온 이슈를 참고했다.

MapStruct와 Lombok을 같이 사용 시 `lombok-mapstruct-binding` 의존성을 추가 해줘야 한다고 되어 있다.

MapStruct 공식 문서에 작성된 코드처럼 작성해도 해결되지 않았는데 biding 버전을 `0.1.0 `에서 `0.2.0` 으로 변경하니 해결되었다.

```
ext {
    mapstructVersion = "1.4.2.Final"
}

dependencies {
    implementation "org.mapstruct:mapstruct:${mapstructVersion}"
    annotationProcessor "org.projectlombok:lombok-mapstruct-binding:0.2.0"
    annotationProcessor "org.mapstruct:mapstruct-processor:${mapstructVersion}"
    testAnnotationProcessor "org.mapstruct:mapstruct-processor:${mapstructVersion}"

    implementation "org.projectlombok:lombok:1.18.16"
    annotationProcessor "org.projectlombok:lombok:1.18.16"
}
```

# Reference

- https://github.com/projectlombok/lombok/issues/2616