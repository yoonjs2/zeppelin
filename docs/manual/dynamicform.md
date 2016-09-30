---
layout: page
title: "Dynamic Form in Apache Zeppelin"
description: "Apache Zeppelin dynamically creates input forms. Depending on language backend, there're two different ways to create dynamic form."
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

# 동적 폼(Form)

<div id="toc"></div>

아파치 제플린은 동적으로 입력 폼이 생성됩니다. 동적 폼을 생성하는 것에는 백엔드 언어에 따른 두 가지 방법이 있습니다. 커스텀 백엔드 언어는 사용하길 원하는 폼 생성 방식을 선택할 수 있습니다.

## 폼 템플릿 사용

이 방식은 간단한 템플릿 언어를 사용하여 폼을 생성하는 방식이며 간단하고 사용하기 쉽습니다. 예를 들어 Markdown, Shell, Spark SQL 백엔드 언어가 이 방식을 사용합니다.

### 텍스트 입력 폼

텍스트 입력 폼을 생성하기 위해서는 `${formName}` 템플릿을 사용합니다. 

다음은 예시입니다. 

<img class="img-responsive" src="/assets/themes/zeppelin/img/screenshots/form_input.png" width="450px" />

또한 `${formName=defaultValue}`를 사용하여 초깃값을 설정할 수 있습니다. 

<img src="../assets/themes/zeppelin/img/screenshots/form_input_default.png" />


### 선택지 폼

선택지 폼을 생성하기 위해서는 `${formName=defaultValue,option1|option2...}`를 사용합니다. 

다음은 예시입니다. 

<img src="../assets/themes/zeppelin/img/screenshots/form_select.png" />

그리고 `${formName=defaultValue,option1(DisplayName)|option2(DisplayName)...}` 를 이용하여 옵션의 이름과 그에 해당하는 값을 각각 설정 할 수 있습니다.

<img src="../assets/themes/zeppelin/img/screenshots/form_select_displayname.png" />

### 체크박스 폼

다중 선택을 위해 `${checkbox:formName=defaultValue1|defaultValue2...,option1|option2...}`을 사용하여 체크박스 폼을 생성할 수 있습니다. 이때 변수는 쉼표로 구분되어있는 선택된 항목에 대한 문자열로 대체될 것입니다. 

다음은 예시입니다. 

<img src="../assets/themes/zeppelin/img/screenshots/form_checkbox.png">

또한 `${checkbox(delimiter):formName=...}`를 사용하여 구분자를 지정할 수 있습니다.

<img src="../assets/themes/zeppelin/img/screenshots/form_checkbox_delimiter.png">

## 프로그래밍적 생성

몇몇 백엔드 언어는 프로그래밍적 방식의 생성 폼을 사용합니다. 예를 들어 [ZeppelinContext](../interpreter/spark.html#zeppelincontext)는 생성 API 폼을 제공합니다. 

다음은 몇가지 예시입니다. 

### 텍스트 입력 폼
<div class="codetabs">
    <div data-lang="scala" markdown="1">

{% highlight scala %}
%spark
println("Hello "+z.input("name"))
{% endhighlight %}

    </div>
    <div data-lang="python" markdown="1">

{% highlight python %}
%pyspark
print("Hello "+z.input("name"))
{% endhighlight %}

    </div>
</div>
<img src="../assets/themes/zeppelin/img/screenshots/form_input_prog.png" />

### 초깃값을 설정해주는 텍스트 입력 폼
<div class="codetabs">
    <div data-lang="scala" markdown="1">

{% highlight scala %}
%spark
println("Hello "+z.input("name", "sun")) 
{% endhighlight %}

    </div>
    <div data-lang="python" markdown="1">

{% highlight python %}
%pyspark
print("Hello "+z.input("name", "sun"))
{% endhighlight %}

    </div>
</div>
<img src="../assets/themes/zeppelin/img/screenshots/form_input_default_prog.png" />

### 선택지 폼
<div class="codetabs">
    <div data-lang="scala" markdown="1">

{% highlight scala %}
%spark
println("Hello "+z.select("day", Seq(("1","mon"),
                                    ("2","tue"),
                                    ("3","wed"),
                                    ("4","thurs"),
                                    ("5","fri"),
                                    ("6","sat"),
                                    ("7","sun"))))
{% endhighlight %}

    </div>
    <div data-lang="python" markdown="1">

{% highlight python %}
%pyspark
print("Hello "+z.select("day", [("1","mon"),
                                ("2","tue"),
                                ("3","wed"),
                                ("4","thurs"),
                                ("5","fri"),
                                ("6","sat"),
                                ("7","sun")]))
{% endhighlight %}

    </div>
</div>
<img src="../assets/themes/zeppelin/img/screenshots/form_select_prog.png" />

#### 체크박스 폼
<div class="codetabs">
    <div data-lang="scala" markdown="1">

{% highlight scala %}
%spark
val options = Seq(("apple","Apple"), ("banana","Banana"), ("orange","Orange"))
println("Hello "+z.checkbox("fruit", options).mkString(" and "))
{% endhighlight %}

    </div>
    <div data-lang="python" markdown="1">

{% highlight python %}
%pyspark
options = [("apple","Apple"), ("banana","Banana"), ("orange","Orange")]
print("Hello "+ " and ".join(z.checkbox("fruit", options, ["apple"])))
{% endhighlight %}

    </div>
</div>
<img src="../assets/themes/zeppelin/img/screenshots/form_checkbox_prog.png" />
