---
layout: page
title: "Writing a new Application(Experimental)"
description: "Apache Zeppelin Application is a package that runs on Interpreter process and displays it's output inside of the notebook. Make your own Application in Apache Zeppelin is quite easy."
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

# 새로운 애플리케이션 만들기 (실습)

<div id="toc"></div>

## 아파치 제플린 애플리케이션은 무엇인가

아파치 제플린 애플리케이션은 인터프리터 프로세스에서 작동하고 `notebook`을 통하여 결과를 보여주는 패키지입니다.
애플리케이션이 인터프리터 프로세스에서 작동하는 동안은 리소스 풀을 통하여 인터프리터가 제공하는 자원에 접근할수 있습니다. 결과는 항상 `AngularDisplaySystem`에 의하여 랜더링됩니다. 그러므로 애플리케이션은 데이터와 모든 인터프리터를 처리 능력을 가지고 있으며, 다방면을 지원할 수 있는 대화형 그래픽 애플리케이션을 제공해야 합니다.


## 사용자 애플리케이션 만들기

애플리케이션을 만든다는 것은 `org.apache.zeppelin.helium.Application`을 상속한다는 것을 의미합니다. jar파일안에 자바클래스 파일들이 포함되어 있는 한, 원하는 IDE나 프로그램 언어를 사용할 수 있습니다. `Application`클래스는 다음과 같이 구성되어 있습니다.

```java

/**
 * 생성자. 애플리케이션이 로드되면 실행된다.
 */
public Application(ApplicationContext context);

/**
 * 필요 리소스안에 업데이트(가능한)사항이 있을 경우 실행된다.
 * 예를 들어 애플리케이션이 로드된 후나 문단이 끝난 후에 실행된다.
 */
public abstract void run(ResourceSet args);

/**
 * 애플리케이션이 언로드되기 전에 실행된다.
 * 애플리케이션은 문단/notbook이 제거되면서 자동적으로 언로드된다.
 */
public abstract void unload();
```

[./zeppelin-examples](https://github.com/apache/incubator-zeppelin/tree/master/zeppelin-examples) 디렉토리에서 애플리케이션에 대한 예제를 확인하실 수 있습니다.


## 개발 모드

개발 모드에서는, IDE에 있는 보통의 자바 애플리케이션처럼 사용자 애플리케이션을 실행할수 있습니다. 그리고 `Zeppelin notebook`안에서 그 결과를 확인할수 있습니다.

`org.apache.zeppelin.interpreter.dev.ZeppelinApplicationDevServer`은 개발 모드 안에서 제플린 애플리케이션을 실행할 수 있게 해줍니다.

```java

// 개발 모드를 위한 시작점
public static void main(String[] args) throws Exception {

  // 개발 모드를 위해 자원을 추가
  LocalResourcePool pool = new LocalResourcePool("dZeppelin notebookev");
  pool.put("date", new Date());

  // 주어진 자원과 함께 개발 모드에서 애플리케이션을 실행
  // 이 경우에, Clock.class.getName()는 애플리케이션 클래스 이름을 가진다.
  ZeppelinApplicationDevServer devServer = new ZeppelinApplicationDevServer(
    Clock.class.getName(), pool.getAll());

  // 개발 모드 시작
  devServer.start();
  devServer.join();
}
```

Zeppelin notebook에서 `%dev run`을 실행하면, 개발모드에서 실행되는 애플리케이션과 연결할 수 있도록 해줍니다.


## 패키지 파일

패키지 파일은 애플리케이션에 대한 정보를 제공하는 json파일입니다.
Json파일은 다음과 같은 정보를 가지고 있습니다.

```
{
  name : "[organization].[name]",
  description : "Description",
  artifact : "groupId:artifactId:version",
  className : "your.package.name.YourApplicationClass",
  resources : [
    ["resource.name", ":resource.class.name"],
    ["alternative.resource.name", ":alternative.class.name"]
  ],
  icon : "<i class="icon"></i>"
}

```

#### name

`name`은 `[group].[name]` 포맷에 포함되어 있는 문자열입니다. 그리고 `[group]`과 `[name]`의 명명은 오직 `[A-Za-z0-9_]`만을 허용합니다.
`group`은 보통 애플리케이션을 제작한 단체의 이름으로 명명합니다.

#### description

애플리케이션에 대한 짧은 설명을 적습니다.

#### artifact

jar 아티펙트의 위치를 적습니다.
`"groupId:artifactId:version"`는 메이븐 저장소로부터 아티펙트를 불러옵니다.
만약 jar파일이 로컬 파일시스템에 존재한다면, 절대/상대경로를 사용하여 작성할 수 있습니다.

예제

메이븐 저장소에 아티펙트가 존재할때 :

```
artifact: "org.apache.zeppelin:zeppelin-examples:0.6.0"
```

로컬 파일시스템에 아티펙트가 존재할때 :

```
artifact: "zeppelin-example/target/zeppelin-example-0.6.0.jar"
```

#### className

애플리케이션의 시작점입니다. `org.apache.zeppelin.helium.Application`을 상속한 클래스입니다.

#### resources

필요 리소스의 이름 또는 클래스이름으로 정의된 2차원 배열을 적습니다. `Helium Application launcher`는 리소스 풀에 존재하는 리소스와 비교를 합니다. 그 다음에 모든 필요 리소스들이 리소스 풀에서 사용 가능함을 확인하게 되면, 애플리케이션에서 사용 가능하게 됩니다.

리소스 이름은 리소스 풀안의 오브젝트들의 이름과 비교할 때 사용됩니다. `className`은 ":"의 접두어를 가진 문자열로 리소스 풀안의 오브젝트들의 `className`과 비교할 때 사용합니다.

애플리케이션은 2개이상의 리소스들이 필요할 수 있습니다. 필요 리소스들은 json파일 안에 나열되어 있습니다. 예를 들어, 만약 애플리케이션이 실행하기 위해서 "name1", "name2" 그리고 "className1"타입의 오브젝트를 요구한다면, resources는 다음과 같이 적으시면 됩니다.

```
resources: [
  [ "name1", "name2", ":className1", ...]
]
```

만약 애플리케이션이 필요 리소스들의 대체 가능한 조합을 처리할수 있다면, 대체 가능한 리소스들은 다음과 같이 나열할 수 있습니다.

```
resources: [
  [ "name", ":className"],
  [ "altName", ":altClassName1"],
  ...
]
```

이러한 방법을 쉽게 이해하는 방법은 다음과 같습니다.

```
resources: [
   [ 'resource' AND 'resource' AND ... ] OR
   [ 'resource' AND 'resource' AND ... ] OR
   ...
]
```


#### icon

아이콘은 애플리케이션 버튼으로 사용됩니다.
이 영역에 적혀있는 문자열은 HTML 태그로 랜더링됩니다.

예제

```
icon: "<i class='fa fa-clock-o'></i>"
```

