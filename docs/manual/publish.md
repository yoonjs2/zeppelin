---
layout: page
title: "How can you publish your paragraph"
description: "Apache Zeppelin provides a feature for publishing your notebook paragraph results. Using this feature, you can show Zeppelin notebook paragraph results in your own website."
group: manual
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

# 제플린 상의 자료를 나의 웹페이지에서 사용하는 방법

<div id="toc"></div>

아파치 제플린은 당신의 'notebook'에 있는 자료를 다른 곳에서 사용할 수 있도록 배포 기능을 제공하고 있습니다. 이 기능을 사용하면 당신은 제플린의 'notebook' 자료를 당신의 웹사이트에서 볼 수 있게 됩니다. 방법은 매우 간단합니다. 단지  `<iframe>` 태그를 당신의 페이지에서 사용하기만 하면 됩니다.

## 자료의 링크 복사하기
당신의 자료를 배포하기 위한 첫 번째 단계는 **자료의 링크를 복사하는 것** 입니다. 

  * 당신의 제플린 'notebook'에 자료가 생성된 후에 오른쪽 옆에 위치한 톱니바퀴 모양의 버튼을 클릭합니다. 그러고 나서 아래 이미지처럼 **Link this Paragraph** 메뉴를 클릭하면 됩니다. 
  
<center><img src="../assets/themes/zeppelin/img/docs-img/link-the-paragraph.png" height="100%" width="100%"></center>
  
  * 제공되는 링크를 복사합니다. 
<center><img src="../assets/themes/zeppelin/img/docs-img/copy-the-link.png" height="100%" width="100%"></center>

## 당신의 웹사이트에 자료 넣기
복사된 자료를 배포하기 위해서는 당신의 웹페이지에 `<iframe>` 태그를 사용하기만 하면 됩니다. 

```
<iframe src="http://< ip-address >:< port >/#/notebook/2B3QSZTKR/paragraph/...?asIframe" height="" width="" ></iframe>
```

최종적으로 당신은 당신의 웹사이트에서 멋진 시각화 결과를 볼 수 있습니다.  
<center><img src="../assets/themes/zeppelin/img/docs-img/your-website.png" height="90%" width="90%"></center>

> **Note**: 자료를 웹페이지에 넣기 위해서는 아파치 제플린이 해당 웹사이트로 연결될 수 있어야 합니다. 그리고 제플린 전체 웹앱은 당신의 사이트에 방문하는 누구나 접근할 수 있으므로 이 기능을 주의깊게 사용해 주시고 믿을 수 있는 환경에서만 사용해 주시길 바랍니다.
