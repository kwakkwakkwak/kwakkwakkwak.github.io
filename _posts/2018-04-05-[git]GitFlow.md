---
title: "[git]GitFlow"
description: "GitFlow 에 대해서 "
categories: Git GitFlow
tags: Git GitFlow
date: 2018-04-05
comments : true
---

# Git Flow는 

![gitFlow 이미지](/assets/images/[git]GitFlow/gitFlow.png)

* Git Flow 의 기본브렌치

> `feature` > `develop` > `release` > `hotfix` > `master`

* `Merge` 순서는 앞에서 뒤로 진행

# 구조와 흐름

* 가장 중심이 되는 브렌치는 `Master`, `develop`
* 머지된 `feature`, `release`, `hotfix` 브렌치는 삭제

## master

* 가장 기본이 되는 브랜치 

* 배포 버전을 관리

## develop

* 개발을 위한 브랜치

* 가장 변화가 많고 개발자들이 업무를 보면 진행하는 브랜치

* 이 브렌치가 안정화되고 배포할 준비가 되면 master 로 병합하고 배포버전 태그 ㄴ

## Feature 

* 기능 개발하는 브랸치 

> * 나오는 곳 : develop
> * 들어가는 곳 : develop
> * 명명 : `master`, `develop`, `release-*`, `hotfix-*` 를 제외하고 가능

## Release 

* 실제 배포 가능한 상태가 될 경우 생성하는 브랜치 

> * 나오는 곳 : `develop`
> * 들어가는 곳 : `develop`, `master`
> * 명명 : `release-*`

## Hotfix

* 긴급 수정을 위한 브랜치

> * 나오는 곳 : `master`
> * 들어가는 곳 : `develop`, `master`
> * 명명 : `hotfix-*`


# 장점

 * 명령어가 나와있다.
 * 왠만한 에디터, ID에 플러그인으로 존재
 
# 단점

 * 브랜치가 많아 복잡하다
 * 안쓰거나 애매한 포지션인 브렌치가 있다.

[참고1](https://ujuc.github.io/2015/12/16/git-flow-github-flow-gitlab-flow/)
[참고2](https://gist.github.com/ihoneymon/a28138ee5309c73e94f9)