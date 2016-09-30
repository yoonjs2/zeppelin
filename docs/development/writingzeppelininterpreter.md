---
layout: page
title: "Writing a New Interpreter"
description: "Apache Zeppelin Interpreter is a language backend. Every Interpreters belongs to an InterpreterGroup. Interpreters in the same InterpreterGroup can reference each other."
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

# 새 인터프리터 작성하기

<div id="toc"></div>

## 아파치 제플린 인터프리터는 무엇인가

아파치 제플린 인터프리터는 백엔드 언어입니다. 예를 들어 제플린에서 스칼라를 사용하기 위해서는, 스칼라 인터프리터가 필요합니다.
각 인터프리터들은 **InterpreterGroup**에 속해 있습니다. 같은 `InterpreterGroup`에 속해 있는 인터프리터들은 각자를 참조할 수 있습니다. 예를 들어, `SparkSqlInterpreter`는 `SparkContext`을 가져오기 위하여 스파크 인터프리터를 참조할 수 있습니다. 단, 같은 그룹안에 인터프리터들이 존재해야 합니다.

<img class="img-responsive" style="width:50%; border: 1px solid #ecf0f1;" height="auto" src="/assets/themes/zeppelin/img/interpreter.png" />

[InterpreterSetting](https://github.com/apache/zeppelin/blob/master/zeppelin-zengine/src/main/java/org/apache/zeppelin/interpreter/InterpreterSetting.java)은 [InterpreterGroup](https://github.com/apache/zeppelin/blob/master/zeppelin-interpreter/src/main/java/org/apache/zeppelin/interpreter/InterpreterGroup.java)을 설정하고 인터프리터의 시작과 종료를 관리합니다.
같은 `InterpreterSetting`을 적용받는 모든 인터프리터들은 단일되고 개별된 JVM위에서 실행됩니다. 인터프리터는 **[Thrift](https://github.com/apache/zeppelin/blob/master/zeppelin-interpreter/src/main/thrift/RemoteInterpreterService.thrift)**을 통하여 제플린 엔진과 소통합니다.

새로운 인터프리터를 만들었을때 **Interpreter Setting** 메뉴에서 `'Separate Interpreter(scoped / isolated) for each note'`모드를 보실수 있으며, 새 인터프린터 인스턴스를 `notebook`당 만드실 수 있습니다. 그러나 같은 `InterpreterSettings`에 적용을 받는 한 같은 JVM위에서 작동합니다.

## 사용자 인터프리터 만들기

새로운 인터프리터를 만드는 것은 매우 간단합니다.
단순히 [org.apache.zeppelin.interpreter](https://github.com/apache/zeppelin/blob/master/zeppelin-interpreter/src/main/java/org/apache/zeppelin/interpreter/Interpreter.java) 추상화 클래스를 상속하고 몇개의 메소드를 구현해주시면 됩니다.
사용자의 빌드 시스템에 `org.apache.zeppelin:zeppelin-interpreter:[VERSION]` 아티펙트를 추가할 수 있습니다. 또한, jar파일들을 특정한 이름을 가진 디렉토리안에 포함할 수 있습니다. 제플린 서버는 인터프리터 디렉토리들을 반복적으로 읽고, 사용자 인터프리터가 포함된 인터프리터들을 초기화합니다.

생성한 인터프리터 그룹, 이름, 여러 정보들을 저장할 수 있는 3가지 장소가 있습니다. 제플린 서버는 현재 위치의 아래를 탐색합니다. 다음으로, 사용자 인터프리터 jar안에 있는 `interpreter-setting.json`을 탐색합니다.

```
{ZEPPELIN_INTERPRETER_DIR}/{YOUR_OWN_INTERPRETER_DIR}/interpreter-setting.json
```

아래에는 사용자 인터프리터를 작성을 위한 `interpreter-setting.json` 예제가 있습니다. 만약 특정 편집기 오브젝트를 지정하지 않은 경우, 인터프리터는 기본 텍스트 모드의 syntax 하이라이팅을 사용할 것입니다.

```json
[
  {
    "group": "your-group",
    "name": "your-name",
    "className": "your.own.interpreter.class",
    "properties": {
      "properties1": {
        "envName": null,
        "propertyName": "property.1.name",
        "defaultValue": "propertyDefaultValue",
        "description": "Property description"
      },
      "properties2": {
        "envName": PROPERTIES_2,
        "propertyName": null,
        "defaultValue": "property2DefaultValue",
        "description": "Property 2 description"
      }, ...
    },
    "editor": {
      "language": "your-syntax-highlight-language"
    }
  },
  {
    ...
  }
]
```

마지막으로, 제플린은 다음과 같이 정적 초기화를 사용합니다.

```
static {
  Interpreter.register("MyInterpreterName", MyClassName.class.getName());
}
```

**정적 초기화는 권장되지 않으며 사라질 것입니다. 이에 대한 지원은 0.6.0버전까지만 될 것입니다.**

인터프리터의 이름은 인터프리터 설정이 진행되는 동안 인터프리터 이름 옵션 박스에 표시됩니다.
인터프리터의 이름은 인터프리터를 이용하여 문단을 해석할때, 확인 작업을 위하여 적습니다.

```
%MyInterpreterName
some interpreter specific code...
```

## 인터프리터를 위한 프로그램 언어들
만약 인터프리터가 특정한 프로그램 언어(스칼라, 파이썬, SQL과 같은)를 이용한다면, `notebook` 문단 편집기에  특정 언어를 위한 syntax 하이라이팅 기능을 추가하는 것을 추천합니다.

지원되는 언어의 리스트를 확인하고 싶다면, `zeppelin-web/bower_components/ace-builds/src-noconflict`안에 `mode-*.js`파일들을 보시거나 [github.com/ajaxorg/ace-builds](https://github.com/ajaxorg/ace-builds/tree/master/src-noconflict)에서 확인하시기 바랍니다.

만약 새로운 syntax 하이라이팅을 추가하고 싶다면, 다음의 단계를 따르셔야 합니다.

1. <code>[zeppelin-web/bower.json](https://github.com/apache/zeppelin/blob/master/zeppelin-web/bower.json)</code> 에 `mode-*.js` 파일을 추가합니다. (빌드될 때, <code>[zeppelin-web/src/index.html](https://github.com/apache/zeppelin/blob/master/zeppelin-web/src/index.html)</code>는 자동적으로 변경됩니다.)
2. `interpreter-setting.json`파일에 `editor` 오브젝트를 추가합니다. 예를 들어, 자바를 사용하고 싶다면, 다음과 같이 적습니다.

  ```
  "editor": {
      "language": "java"
  }
  ```

## 사용자 인터프리터 바이너리 설치하기

사용자 인터프리터를 빌드하게 되면, 인터프리터 디렉토리안에  모든 의존성 라이브러리들과 빌드 결과물을 함께 두실수 있습니다.

```
[ZEPPELIN_HOME]/interpreter/[INTERPRETER_NAME]/
```

## 사용자 인터프리터 설정하기

사용자 인터프리터를 설정하시려면 다음의 단계를 따르셔야 합니다 :

1. 제플린에 사용자 인터프리터 클래스 이름을 `conf/zeppelin-site.xml`안에 있는 zeppelin.interpreters 프로퍼티에 추가하세요.
  프로퍼티 값은 [INTERPRETER\_CLASS\_NAME]의 형태로 콤마( , )로 분리되어야 합니다.
  예를 들어, 다음과 같이 사용합니다.

  ```
  <property>
    <name>zeppelin.interpreters</name>
    <value>org.apache.zeppelin.spark.SparkInterpreter,org.apache.zeppelin.spark.PySparkInterpreter,org.apache.zeppelin.spark.SparkSqlInterpreter,org.apache.zeppelin.spark.DepInterpreter,org.apache.zeppelin.markdown.Markdown,org.apache.zeppelin.shell.ShellInterpreter,org.apache.zeppelin.hive.HiveInterpreter,com.me.MyNewInterpreter</value>
  </property>
  ```

2. 사용자 인터프리터를 [default configuration](https://github.com/apache/zeppelin/blob/master/zeppelin-zengine/src/main/java/org/apache/zeppelin/conf/ZeppelinConfiguration.java#L397)에 추가하세요. 이것은 `zeppelin-site.xml`가 없을 경우 참조됩니다.

3. 다음을 실행하면 제플린이 시작됩니다. `./bin/zeppelin-daemon.sh start`.

4. 인터프리터 페이지에서 `+Create`버튼을 클릭하고 자신의 인터프리터 프로퍼티들을 설정합니다.
이제 사용자 인터프리터를 시작할 준비가 되셨습니다.

> **알림 :** 제플린과 같이 배포되는 인터프리터들은 [default configuration](https://github.com/apache/zeppelin/blob/master/zeppelin-zengine/src/main/java/org/apache/zeppelin/conf/ZeppelinConfiguration.java#L397)을 가지고 있습니다. 이것은 `conf/zeppelin-site.xml`가 존재하지 않을 시에 참조됩니다.

## 사용자 인터프리터 사용하기
## Configure your interpreter

### 0.5.0
`notebook`안에, `%[INTERPRETER_NAME]` 디렉토리는  사용자 인터프리터를 호출합니다.
`zeppelin.interpreters`안에 첫번째로 설정된 인터프리터는 기본적인 인터프리터로 사용될 것입니다.

예를 들어, 다음과 같이 사용합니다.

```
%myintp

val a = "My interpreter"
println(a)
```

### 0.6.0 과 그 후의 버전
`notebook`안에, `%[INTERPRETER_GROUP].[INTERPRETER_NAME]` 디렉토리는 사용자 인터프리터를 호출합니다.

[INTERPRETER\_GROUP] 또는 [INTERPRETER\_NAME]를 생략하실 수 있습니다.
만약 [INTERPRETER\_NAME]을 생략하면, [INTERPRETER\_GROUP]에서 첫번째로 사용 가능한 인터프리터가 선택됩니다.
이와 같이, [INTERPRETER\_GROUP]을 생략하게 되면, [INTERPRETER\_NAME]이 기본 인터프리터 그룹으로부터 선택됩니다.

만약 mygrp그룹안에 myintp1와 myintp2의 인터프리터가 있다면, 다음과 같이 사용하면 myintp1를 호출하게 됩니다.

```
%mygrp.myintp1

codes for myintp1
```

그리고 다음과 같이 사용하게되면, myintp2이 호출됩니다.

```
%mygrp.myintp2

codes for myintp2
```

만약 인터프리터 이름을 생략한다면, 그룹안의 첫번째로 사용 가능한 인터프리터가 선택됩니다(myintp1).

```
%mygrp

codes for myintp1

```

기본 그룹으로 사용자 인터프리터 그룹을 지정하였을 때, 사용자 인터프리터 그룹은 생략 가능합니다.

```
%myintp2

codes for myintp2
```

## 예제

제플린과 같이 배포되는 기본적인 인터프리터들을 확인하세요.

 - [spark](https://github.com/apache/zeppelin/tree/master/spark)
 - [markdown](https://github.com/apache/zeppelin/tree/master/markdown)
 - [shell](https://github.com/apache/zeppelin/tree/master/shell)
 - [jdbc](https://github.com/apache/zeppelin/tree/master/jdbc)

## 제플린에 새로운 인터프리터 기여하기

제플린은 새로운 인터프리터를 환영합니다. 다음의 단계를 따라주시기 바랍니다.

 - 첫째,  [여기](https://zeppelin.apache.org/contribution/contributions.html)의 contribution guide를 확인합니다.
 - [사용자 인터프리터 만들기](##make-your-own-interpreter)의 단계들을 따릅니다.
 - [사용자 인터프리터 설정하기](#configure-your-interpreter)에 따라 사용자 인터프리터를 추가합니다. 또한 사용자 인터프리터에 대한 예제를 [zeppelin-site.xml.template](https://github.com/apache/zeppelin/blob/master/conf/zeppelin-site.xml.template)에 추가합니다.
 - 테스트를 추가해야 합니다! 모든 변경에 대하여 [Travis](https://travis-ci.org/apache/zeppelin)에서 실행되어야 합니다. 그리고 그것이 자기 완성형이어야 하는 것은 중요합니다.
 - 사용자 인터프리터를 [`pom.xml`](https://github.com/apache/zeppelin/blob/master/pom.xml)의 모듈로써 추가해야 합니다.
 - 어떻게 인터프리터가 실행되는지에 대하여 `docs/interpreter/`아래에 문서를 추가해야 합니다. [예제](https://github.com/apache/zeppelin/blob/master/docs/interpreter/elasticsearch.md)를 참고하시고 이에 따라 마크다운 스타일을 사용해 주셔야 합니다. 인터프리터에 대한 환경설정을 리스트화 하고 사용자 인터프리터의 실행 예제들이 마크다운의 코드 박스 형태로 제공되어야 합니다. 적절한 이미지들을 링크해주시기 바랍니다.(이미지들은 `docs/assets/themes/zeppelin/img/docs-img/`에 위치해야 합니다.). 네비게이션 메뉴에(`docs/_includes/themes/zeppelin/_navigation.html`) 사용자 문서에 대한 링크를 추가하셔야 합니다.
 - 모든 의존성 라이브러리들과 이와 연관된 라이브러리들의 라이센스들을 확인하시고, [license file](https://github.com/apache/zeppelin/blob/master/zeppelin-distribution/src/bin_license/LICENSE)에 리스트화하여 주시는 것은 매우 중요합니다.
 - 변경사항을 커밋하시고 [Mirror on GitHub](https://github.com/apache/zeppelin) 프로젝트에 [Pull Request](https://github.com/apache/zeppelin/pulls)를 올려주시기 바랍니다. Travis CI 빌드가 통과함을 체크해주셔야 합니다.
