---
layout: page
title: "Manual Zeppelin version upgrade procedure"
description: "This document will guide you through a procedure of manual upgrade your Apache Zeppelin instance to a newer version. Apache Zeppelin keeps backward compatibility for the notebook file format."
group: install
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

# 제플린 업그레이드 가이드

<div id="toc"></div>

기본적으로 새로운 버전의 제플린은 이전 버전의 환경설정과 notebook 폴더와 호환이 됩니다. 그러므로 이전 버전의 'notebook'과 'conf' 폴더를 새 버전에 복사해 두는걸로 충분합니다.

## 업그레이드 방법
1. 제플린을 종료시킵니다.

    ```
    bin/zeppelin-daemon.sh stop
    ```

2. `notebook` 과 `conf` 폴더를 백업 폴더에 복사해둡니다.

3. 새로운 버전의 제플린을 다운로드 받고 설치합니다. 참고 :[설치 가이드](./install.html#installation).

4. 백업해둔 `notebook` 과 `conf`폴더를 새로운 버전의 제플린의 `notebook` 과 `conf`폴더에 붙여넣기 합니다.


5. 제플린을 실행시킵니다.
   ```
   bin/zeppelin-daemon.sh start
   ```

## 마이그레이션 시 참고사항

### 제플린 0.6에서 0.7로 업그레이드 하기 
- 0.7에서는 `ZEPPELIN_INTP_JAVA_OPTS`의 초기 값으로 `ZEPPELIN_JAVA_OPTS`을 사용하지 않습니다. 이는 `ZEPPELIN_MEM`/`ZEPPELIN_INTP_MEM`에서도 동일합니다. 그러므로 만일 interpreter process에서 the jvm opts를 설정하고 싶다면 `ZEPPELIN_INTP_JAVA_OPTS` 과 `ZEPPELIN_INTP_MEM`를 명확하게 설정 해주시길 바랍니다.

- `%prefix`에 `%jdbc(prefix)`를 적용하는 것은 더 이상 불가능합니다.대신 %[interpreter alias]를 GUI에서 다수의 interpreter 설정을 하는 데 사용 하실 수 있습니다. 
 
