---
layout: default
title: Git pull error
parent: Git-Error
grand_parent: Error
---

# Git pull error

원격 레파지토리를 받아올 때(pull) 다음과 같은 에러가 발생하였다.

```
error: Your local changes to the following files would be overwritten by merge: deploy.sh
Please commit your changes or stash them before you merge.
```

오류 원인은 로컬 소스의 변경 사항을 처리하지 않아서이다.

해결 방법은 오류 메시지에 나와 있듯이 변경 사항을 `commit` 하거나 `stash` 명령어 통해 해결할 수 있다.

변경사항을 commit 하고 싶지 않기 때문에 stash로 해결해 보았다.

`stash`는 변경사항을 커밋하지 않고 임시 공간에 저장하여 나중에 다시 꺼내와 작업을 할 수 있다.

```
git stash
```

이 때 `working directory`는 변경사항이 없는 깨끗한 상태가 된다.

원격 레파지토리를 다시 받아오면 오류가 발생하지 않는걸 확인할 수 있다.

임시 공간에 저장된 변경 사항 목록을 확인할 수 있다.

```
git stash list
```

변경사항을 다시 가져와 적용하거나 제거할 수 있다.

```
# 가장 최근에 저장된 사항을 적용한다.
git stash apply

# 가장 최근에 저장된 사항을 제거한다.
git stash drop
```