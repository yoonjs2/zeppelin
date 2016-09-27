---
layout: page
title: "Contributing to Apache Zeppelin (Code)"
description: "How can you contribute to Apache Zeppelin project? This document covers from setting up your develop environment to making a pull request on Github."
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

# 아파치 제플린에 기여하기 (코드)

<div id="toc"></div>

> **알림 :** 아파치 제플린은 [Apache2 License](http://www.apache.org/licenses/LICENSE-2.0.html) 을 사용하는 소프트웨어입니다.
제플린으로의 기여들(소스 코드, 문서들, 이미지, 웹사이트)은 Apache2 License를 준수함을 암묵적으로 동의한 것입니다.

## 설치
다음은 제플린을 빌드하고 테스트할때 필요한 툴들을 소개합니다.

#### 소프트웨어 구성 관리 (Software Configuration Management [SCM])

제플린은 git을 소프트웨어 구성 관리(SCM)시스템으로써 사용하고 있습니다. 이에 따라, 개발 머신에 git client를 설치하셔야 합니다.

#### 통합 개발 환경 (Integrated Development Environment [IDE])

사용하고 싶은 IDE나 커멘드 라인 편집기들은 모두 사용 가능합니다.

#### 빌드 툴

코드를 빌드하기 위해서는 다음의 사항을 설치하셔야 합니다.

  * Oracle Java 7
  * Apache Maven

## 소스 코드 가져오기
우선, 제플린의 소스 코드를 가져와야 합니다.
제플린의 공식 소스 코드 배포 사이트는 다음과 같습니다.
[http://git.apache.org/zeppelin.git](http://git.apache.org/zeppelin.git)

### git 접속

git을 사용하여 자신의 개발 머신에 소스 코드를 가져 옵니다.

```
git clone git://git.apache.org/zeppelin.git zeppelin
```

당신이 특정한 branch에 대하여 개발을 하고 싶다면(예를 들어 branch-0.5.6) 다음과 같이 실행해야 합니다.

```
git clone -b branch-0.5.6 git://git.apache.org/zeppelin.git zeppelin
```

아파치 제플린은 소스 관리 작업을 [Fork & Pull](https://github.com/sevntu-checkstyle/sevntu.checkstyle/wiki/Development-workflow-with-Git:-Fork,-Branching,-Commits,-and-Pull-Request)에 따라 사용하고 있습니다.
만약 제플린을 빌드하는 것뿐만 아니라 수정하고 싶다면, [Zeppelin github mirror repository](https://github.com/apache/zeppelin)를 Fork하고 pull request를 보내셔야 합니다.

### 빌드

```
mvn install
```

테스트를 건너뛰고 싶다면 다음과 같이 실행하셔야 합니다.

```
mvn install -DskipTests
```

특정 스파크 / 하둡 버전과 같이 빌드하시려면 다음과 같이 실행하셔야 합니다.

```
mvn install -Dspark.version=x.x.x -Dhadoop.version=x.x.x
```


### 제플린 서버를 개발 모드에서 실행하기

```
cd zeppelin-server
HADOOP_HOME=YOUR_HADOOP_HOME JAVA_HOME=YOUR_JAVA_HOME mvn exec:java -Dexec.mainClass="org.apache.zeppelin.server.ZeppelinServer" -Dexec.args=""
```

> **알림:** 제플린 루트 디렉토리에서 ```mvn clean install -DskipTests```를 가장 먼저 실행시켜야 합니다. 그렇지 않으면, 서버는 로컬 레파지토리으로부터 필수적인 의존성 라이브러리들을 찾지 못하고 빌드에 실패하게 됩니다.

또는 데몬 스크립트를 사용하세요.

```
bin/zeppelin-daemon start
```

서버는 [http://localhost:8080](http://localhost:8080)에서 실행됩니다.

### Thrift 코드 생성하기

제플린 코드의 일부분은 [Thrift](http://thrift.apache.org)에 의해 생성됩니다.
대부분의 제플린 관련 변경은 걱정하실 필요없습니다. 그러나 만약 Thrift IDL 파일들(예를 들어 zeppelin-interpreter/src/main/thrift/*.thrift)을 변경하셨다면, 이 파일들을 다시 생성해야 합니다. 그리고 이 파일들의 업데이트 버전들을 패치버전에 첨부하고 보내야 합니다.

코드를 재생성하기 위해서는, **thrift-0.9.2**를 설치하고 제플린 소스 디렉토리안으로 디렉토리를 변경해야 합니다. 그리고 다음의 코드를 실행해야 합니다.

```
thrift -out zeppelin-interpreter/src/main/java/ --gen java zeppelin-interpreter/src/main/thrift/RemoteInterpreterService.thrift
```

## 어디서부터 시작해야 하는가
초보자들을 위한 이슈들은 <a href="https://issues.apache.org/jira/browse/ZEPPELIN-981?jql=project%20%3D%20ZEPPELIN%20AND%20labels%20in%20(beginner%2C%20newbie)">여기</a>에서 찾을수 있습니다.

## 지속적으로 참여하기
기여자들은 제플린 메일링 리스트에 가입해야 합니다.

* [dev@zeppelin.apache.org](http://mail-archives.apache.org/mod_mbox/zeppelin-dev/) 는 제플린에 코드를 기여하고 싶은 사람들을 위한 공간입니다. ([구독](mailto:dev-subscribe@zeppelin.apache.org?subject=send this email to subscribe), [구독 취소](mailto:dev-unsubscribe@zeppelin.apache.org?subject=send this email to unsubscribe), [아카이브](http://mail-archives.apache.org/mod_mbox/zeppelin-dev/))

만약 여러분이 생각하고 있는 이슈들이 있다면, [JIRA](https://issues.apache.org/jira/browse/ZEPPELIN)에 가서 `ticket`을 생성하시기 바랍니다.
