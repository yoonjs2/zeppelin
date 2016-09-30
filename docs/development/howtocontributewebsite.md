---
layout: page
title: "Contributing to Apache Zeppelin (Website)"
description: "How can you contribute to Apache Zeppelin project website? This document covers from building Zeppelin documentation site to making a pull request on Github."
group: development
---
<!--
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->
{% include JB/setup %}

# 아파치 제플린에 기여하기 (웹사이트)

<div id="toc"></div>

이 페이지는 어떻게 아파치 제플린을 빌드하고 아파치 제플린의 문서에 기여할 수 있는지에 대한 개요를 보여줍니다.
[zeppelin.apache.org](https://zeppelin.apache.org/docs/latest/)에 있는 온라인 문서는 여기에 있는 파일들로부터 만들어집니다.

> **알림 :** 아파치 제플린은 [Apache2 License](http://www.apache.org/licenses/LICENSE-2.0.html) 을 사용하는 소프트웨어입니다.
제플린으로의 기여들(소스 코드, 문서들, 이미지, 웹사이트)은 Apache2 License를 준수함을 암묵적으로 동의한 것입니다.

## 소스 코드 가져오기
우선, 제플린의 소스 코드를 가져와야 합니다.
제플린의 공식 소스 코드 배포 사이트는 다음과 같습니다.
[http://git.apache.org/zeppelin.git](http://git.apache.org/zeppelin.git)

웹 사이트 문서들은 'master' branch가 가지고 있는 `/docs` 디렉토리에 있습니다.

### git 접속

우선, 웹 사이트의 소스 코드가 필요합니다.

제플린의 공식 미러 사이트는 다음과 같습니다.

[http://git.apache.org/zeppelin.git](http://git.apache.org/zeppelin.git)

git을 사용하여 개발 머신에 소스 코드를 가져와야 합니다.

```
git clone git://git.apache.org/zeppelin.git
cd docs
```
아파치 제플린은 소스 관리 작업을 [Fork & Pull](https://github.com/sevntu-checkstyle/sevntu.checkstyle/wiki/Development-workflow-with-Git:-Fork,-Branching,-Commits,-and-Pull-Request)에 따라 사용하고 있습니다.
만약 제플린을 빌드하는 것뿐만 아니라 수정하고 싶으시다면, [Zeppelin github mirror repository](https://github.com/apache/zeppelin)를 Fork하고 pull request를 보내셔야 합니다.

### 빌드

코드를 빌드하기 위해서는 미리 설치해야 하는 사항들이 있습니다. [docs/README.md](https://github.com/apache/zeppelin/blob/master/docs/README.md)에 있는 [빌드 문서](https://github.com/apache/zeppelin/blob/master/docs/README.md#build-documentation)를 참조해 주시기 바랍니다.

### 웹 사이트를 개발 모드에서 실행하기

웹 사이트를 수정하시면서 생긴 변경사항들의 프리뷰를 확인하실수 있습니다.
[docs/README.md](https://github.com/apache/zeppelin/blob/master/docs/README.md)에 있는 [웹 사이트 실행하기](https://github.com/apache/zeppelin/blob/master/docs/README.md#run-website)를 참조해 주시기 바랍니다.
위의 내용을 참조 하신 후에, 웹 브라우저에서 [http://localhost:4000](http://localhost:4000)에 접속하시면 프리뷰를 보실 수 있습니다.

### Pull Request 만들기

수정이 완료되신 후, `pull-request`를 보내시면 됩니다.


## 다른 방안

github의 웹 인터페이스에 있는 `/docs/` 디렉토리안에 있는 `.md` 파일들을 직접 수정하시고 `pull-request`를 보내실 수 있습니다.

## 지속적으로 참여하기
기여자들은 제플린 메일링 리스트에 가입해야 합니다.

* [dev@zeppelin.apache.org](http://mail-archives.apache.org/mod_mbox/zeppelin-dev/) 는 제플린에 코드를 기여하고 싶은 사람들을 위한 공간입니다. ([구독](mailto:dev-subscribe@zeppelin.apache.org?subject=send this email to subscribe), [구독 취소](mailto:dev-unsubscribe@zeppelin.apache.org?subject=send this email to unsubscribe), [아카이브](http://mail-archives.apache.org/mod_mbox/zeppelin-dev/))

만약 여러분이 생각하고 있는 이슈들이 있다면, [JIRA](https://issues.apache.org/jira/browse/ZEPPELIN)에 가서 `ticket`을 생성하시기 바랍니다.
