---
layout: page
title: "Apache Zeppelin Tutorial"
description: "This tutorial page contains a short walk-through tutorial that uses Apache Spark backend. Please note that this tutorial is valid for Spark 1.3 and higher."
group: quickstart
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

# 제플린 튜토리얼

<div id="toc"></div>

이 튜토리얼은 핵심 제플린 개념의 일부를 소개합니다. 튜토리얼에 들어가기 전에 제플린을 먼저 설치해야합니다.그렇지 않으면 [이 곳](../install/install.html)을 먼저 참조합니다.

현재 제플린의 주요 백엔드 처리 엔진은 [Apache Spark](https://spark.apache.org)입니다. 이 시스템이 처음이라면, 스파크가 제플린을 최대한 활용하기 위해 데이터를 어떻게 처리하는 지에 대한 방안을 가지고 시작하길 원할 것입니다.

## 로컬 파일을 이용한 튜토리얼

### 데이터 정제

튜토리얼을 시작하기 전에, [bank.zip](http://archive.ics.uci.edu/ml/machine-learning-databases/00222/bank.zip)을 먼저 다운로드 받습니다.

우선, csv 형식 데이터를 Bank 객체의 RRD로 변환하기 위해 아래 스크립트를 실행한다. 또한 filter 함수를 사용해서 헤더를 제거합니다.

```scala

val bankText = sc.textFile("yourPath/bank/bank-full.csv")

case class Bank(age:Integer, job:String, marital : String, education : String, balance : Integer)

// 각 라인을 분리하여 "age"로 시작하는 헤더를 걸러내고, 'Bank' case class로 매핑합니다.
val bank = bankText.map(s=>s.split(";")).filter(s=>s(0)!="\"age\"").map(
    s=>Bank(s(0).toInt, 
            s(1).replaceAll("\"", ""),
            s(2).replaceAll("\"", ""),
            s(3).replaceAll("\"", ""),
            s(5).replaceAll("\"", "").toInt
        )
)

// DataFrame으로 변환하고 임시 테이블을 테이블을 생성합니다.
bank.toDF().registerTempTable("bank")
```

### 데이터 검색

bank의 나이 분포를 확인하려면, 아래를 실행합니다.

```sql
%sql select age, count(1) from bank where age < 30 group by age order by age
```

`30`을 `${maxAge=30}`으로 대체해서 나이 조건을 설정하는 입력 상자를 만들 수 있습니다.

```sql
%sql select age, count(1) from bank where age < ${maxAge=30} group by age order by age
```

혼인 여부를 포함한 나이 분포를 확인하고, 혼인 여부를 선택할 선택 박스를 추가하려면, 아래를 실행합니다.

```sql
%sql select age, count(1) from bank where marital="${marital=single,single|divorced|married}" group by age order by age
```

<br />
## 스트리밍 데이터를 이용한 튜토리얼 

### 데이터 정제

이 튜토리얼은 트위터의 샘플 트윗 스트림을 기반으로 하기때문에, 트위터 계정으로 인증이 되어야합니다. 인증하기 위해, [Twitter Credential Setup](https://databricks-training.s3.amazonaws.com/realtime-processing-with-spark-streaming.html#twitter-credential-setup)을 참조합니다. API 키를 받은 후, 아래 스크립트에 자격 증명 관련 값(`apiKey`, `apiSecret`, `accessToken`, `accessTokenSecret`)을 API 키로 채워야합니다. 
아래 스크립트는 Tweet 객체의 RDD를 생성하고, 스트림 데이터를 테이블로 등록합니다.

```scala
import org.apache.spark.streaming._
import org.apache.spark.streaming.twitter._
import org.apache.spark.storage.StorageLevel
import scala.io.Source
import scala.collection.mutable.HashMap
import java.io.File
import org.apache.log4j.Logger
import org.apache.log4j.Level
import sys.process.stringSeqToProcess

/** 트위터에 접근하기 위한 Oauth 자격 증명을 구성 */
def configureTwitterCredentials(apiKey: String, apiSecret: String, accessToken: String, accessTokenSecret: String) {
  val configs = new HashMap[String, String] ++= Seq(
    "apiKey" -> apiKey, "apiSecret" -> apiSecret, "accessToken" -> accessToken, "accessTokenSecret" -> accessTokenSecret)
  println("Configuring Twitter OAuth")
  configs.foreach{ case(key, value) =>
    if (value.trim.isEmpty) {
      throw new Exception("Error setting authentication - value for " + key + " not set")
    }
    val fullKey = "twitter4j.oauth." + key.replace("api", "consumer")
    System.setProperty(fullKey, value.trim)
    println("\tProperty " + fullKey + " set as [" + value.trim + "]")
  }
  println()
}

// 트위터 자격증명 구성
val apiKey = "xxxxxxxxxxxxxxxxxxxxxxxxx"
val apiSecret = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
val accessToken = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
val accessTokenSecret = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
configureTwitterCredentials(apiKey, apiSecret, accessToken, accessTokenSecret)

import org.apache.spark.streaming.twitter._
val ssc = new StreamingContext(sc, Seconds(2))
val tweets = TwitterUtils.createStream(ssc, None)
val twt = tweets.window(Seconds(60))

case class Tweet(createdAt:Long, text:String)
twt.map(status=>
  Tweet(status.getCreatedAt().getTime()/1000, status.getText())
).foreachRDD(rdd=>
  // B아래 코드는 spark 1.3.0에서만 작동합니다.
  // spark 1.1.x and spark 1.2.x 에서는
  // rdd.registerTempTable("tweets") 을 사용해야합니다.
  rdd.toDF().registerAsTable("tweets")
)

twt.print

ssc.start()
```

### 데이터 검색

아래 각 스크립트는 실시간 데이터를 기반으로하기때문에 실행 버튼을 클릭할 때마다 다른 결과값을 출력합니다.

단어 **girl**을 포함하는 최대 10개의 트윗을 추출해봅시다.

```sql
%sql select * from tweets where text like '%girl%' limit 10
```

지난 60초 동안 초당 얼마나 많은 트윗이 생성되었는지 확인해봅시다.

```sql
%sql select createdAt, count(1) from tweets group by createdAt order by createdAt
```


또한, 사용자 정의 함수를 만들어서 스파크 SQL에서 사용할 수도 있습니다. `sentiment`라는 함수를 만들어서 연습해봅시다. 이 함수는 파라미터에 대하여 세 가지 속성(긍정, 부정, 중립) 중 하나를 반환합니다.

```scala
def sentiment(s:String) : String = {
    val positive = Array("like", "love", "good", "great", "happy", "cool", "the", "one", "that")
    val negative = Array("hate", "bad", "stupid", "is")
    
    var st = 0;

    val words = s.split(" ")    
    positive.foreach(p =>
        words.foreach(w =>
            if(p==w) st = st+1
        )
    )
    
    negative.foreach(p=>
        words.foreach(w=>
            if(p==w) st = st-1
        )
    )
    if(st>0)
        "positivie"
    else if(st<0)
        "negative"
    else
        "neutral"
}

// 아래 코드는 spark 1.3.0에서만 작동합니다.
// spark 1.1.x and spark 1.2.x 에서는 
// sqlc.registerFunction("sentiment", sentiment _) 을 사용해야합니다.
sqlc.udf.register("sentiment", sentiment _)

```

위에서 만든 `sentiment` 함수를 사용하여 사람들이 'girl'에 대해 어떻게 생각하는지 확인하기위해 아래를 실행합니다.

```sql
%sql select sentiment(text), count(1) from tweets where text like '%girl%' group by sentiment(text)
```