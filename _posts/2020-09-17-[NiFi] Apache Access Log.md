---
title: "[NiFi] Apache Access Log 연동"
date: 2020-09-17 00:00:00 -0000

tagline: "by baeksy"
header:
  overlay_image: /assets/images/splash/nifi.png
  overlay_filter: 0.5
  #show_overlay_excerpt : false

toc: true
toc_label: "Apache Access Log 연동"
toc_sticky: true

categories: 
  - NiFi
tags: 
  - Bigdata
description: Apache Access Log 파일을 가져와 정형화하고 Elasticsearch에 쌓는 방법을 설명하는 글입니다.
---
이 글은 NiFi를 활용하여 Apache Access Log를 읽어와 정형화하고 Elasticsearch에 연동하는 방법을 설명하는 글이다. NiFi에 대해 한글로 example과 한글로 번역된 글이 부족하여 많은 시행착오를 거쳤다.

## Apache Access Log 생성

[Github](https://github.com/kiritbasu/Fake-Apache-Log-Generator)에서 Apache Access Log를 생성하는 파일을 다운받는다.

### 기본적인 사용법

아래를 실행하면 5초 간격으로 로그를 무한히 생성한다.

  ```shell
  python apache-fake-log-gen.py -n 0 -s 5 -o LOG 
  ```

- -n : 샘플 로그의 갯수(0을 입력히 무한히 생성)
- -s : 라인을 생성하는 주기(seconds)
- -o : output file의 타입(LOG, GZ, CONSOLE)
- -l : CLF(공통 로그 포맷으로 출력)

## Apache Access Log 연동 설명

### 전체 화면
Apache Access Log를 생성하여 한줄씩 읽어온다. 읽어온 데이터를 Grok Expression을 활용하여 파싱하고, 이를 json 형태로 flowfile의 content에 저장한다. json으로 변환된 데이터를 Elasticsearch에 연동하고 이를 console에서 확인한다.

![total-view]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/total-view.png){: .align-center}

### TailFile

생성된 로그를 한줄씩 읽어오는 Processor, TailFile Processor는 새로 생성된 로그만 읽어오기 때문에 로그를 생성하는 python파일을 무한히 생성시킨 다음 확인한다.

![tailfile]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/tailfile-processor.png){: .align-center}

- Tailing mode : Single file / Multiple files 옵션을 선택
- File(s) to Tail : 읽어올 파일의 위치,  Single file일 경우 전체 경로를 입력
- Base directory : Multiple files의 경우 기본 경로를 입력, Single file의 경우 입력할 필요 없음

### ExtractGrok

읽어온 log를 Grok Expression으로 파싱해주는 Processor

![extractgrok]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/extractgrok-processor.png){: .align-center}

- [Grok Expression](https://nifi.apache.org/docs/nifi-docs/components/org.apache.nifi/nifi-record-serialization-services-nar/1.5.0/org.apache.nifi.grok.GrokReader/additionalDetails.html) : grok expresson으로 파싱할 내용을 작성 (grok expression에서는 자주 사용하는 기본형을 제공)

Grok Expression으로 FlowFile의 Content를 파싱하면 Attribute들은 아래와 같이 바뀐다. grok으로 시작하는 attribute가 추가된 것을 확인할 수 있다.

**파싱 전 FlowFile의 Attribute**

![before-attributes]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/before-attributes.png){: .align-center}

**파싱 후 FlowFile의 Attribute**

![after-attributes]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/after-attributes.png){: .align-center}

### UpdateAttribute

FlowFile의 Attribute를 업데이트 해주는 Processor, 생성할 Attribute는 우측 상단의 Add Property버튼을 클릭하여 작성한다.

![updateattribute]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/updateattribute-processor.png){: .align-center}

- Delete Attributes Expression : 정규표현식을 이용하여 삭제할 Attribute를 지정

이후 필요한 내용의 Attirbute들을 추가, 수정, 삭제하면 된다.

**업데이트 후 FlowFile의 Attribute**

![update-attibute]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/update-attribute.png){: .align-center}

### AttributesToJSON

FolwFile의 Attribute를 JSON으로 변환해주는 Processor

![attributestojson]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/attributestojson-processor.png){: .align-center}

- Destination : flowfile-attribute, flowfile-content 옵션이 존재하여 변한된 json을 attibute에 저장할 것인지, content에 저장할 것인지를 설정
- Attributes List: 콤마(,)를 기반으로 json으로 변경할 속성(attribute)를 추출할 수 있다.

**변환 전 FlowFile의 Content**

```shell
157.239.156.249 - - [10/Jul/2020:14:24:29 +0900] "GET /explore HTTP/1.0" 200 4976 "http://blair.com/author/" "Mozilla/5.0 (iPhone; CPU iPhone OS 9_3_6 like Mac OS X) AppleWebKit/531.1 (KHTML, like Gecko) CriOS/17.0.860.0 Mobile/58W977 Safari/531.1"
```

**변환 후 FlowFile의 Content**

```shell
{
  "referrer" : "http://blair.com/author/",
  "agent" : "Mozilla/5.0 (iPhone; CPU iPhone OS 9_3_6 like Mac OS X) AppleWebKit/531.1 (KHTML, like Gecko) CriOS/17.0.860.0 Mobile/58W977 Safari/531.1",
  "hostname" : "157.239.156.249",
  "bytes" : "4976",
  "response" : "200",
  "clientip" : "157.239.156.249",
  "verb" : "GET",
  "httpversion" : "1.0",
  "quotedstring" : "[http://blair.com/author/, Mozilla/5.0 (iPhone; CPU iPhone OS 9_3_6 like Mac OS X) AppleWebKit/531.1 (KHTML, like Gecko) CriOS/17.0.860.0 Mobile/58W977 Safari/531.1]",
  "uuid" : "1a0bfc2e-63b8-4419-98c8-b55d1e75139d",
  "timestamp" : "10/Jul/2020:14:24:29 +0900"
}
```

### PutElasticsearchHttp

JSON으로 변환된 FlowFile의 Content를 Elasticsearch에 전송해주는 Processor

![putelasticsearchhttp]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/putelasticsearchhttp-processor-1.png){: .align-center}

![putelasticsearchhttp]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/putelasticsearchhttp-processor-2.png){: .align-center}

- Elasticsearch URL : 연동할 Elasticsearch의 URL

- Index : Elasticseach로 연동될 데이터의 index(ex apache-yyyy.MM.dd)

  [NiFi Expression Language](https://nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html#strings) : NiFi에서 Attribute를 조작하는 기능을 제공

- Type : Elasticseach로 연동될 데이터의 type

### LogAttribute

FlowFile 정보를 NiFi로그 파일 ($NIFI_HOME/logs/nifi-app.log)에 출력해주는 Processor

![logattribute]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/logattribute-processor.png){: .align-center}

## Connection 연결시 주의사항

Processor를 실행시킬 때 해당 Processor의 Connection이 모두 연결되어 있지 않은 상태에서는 아래의 그림과 같이 오류를 발생하게 된다. 

![relationship]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/relationship-1.png){: .align-center}

이를 해결하기위해서는 연결되지 않는 Connetion을 Automatically Terminate Relationships에 체크를 해주어야 한다.

![relationship]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/relationship-2.png){: .align-center}

## Queue 데이터 확인 방법

각 Processor간 Queue를 우클릭하여 Configure에 들어간다. Configure에서는 queue의 이름, 우선순위 등을 설정 할 수 있다.

![queue]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/queue-2.png){: .align-center}

- FlowFile Expiration : object가 flow에서 존재할 수 있는 최대 시간
- Back Pressure Object Threshold : queue에 존재할 수 있는 object의 수
- Size Threshold : queue의 최대 data size
- Available Prioritizers : queue에서 처리할 우선순위
- Load Balance Strategy : 이 connection의 데이터를 클러스터의 노드에서 load balance하는 방법

List Queue를 클릭하면 해당 queue에 존재하는 FlowFile의 Attribute와 Content를 확인할 수 있다.

![queue]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/queue-3.png){: .align-center}

## Elasticsearch 저장 index 확인

아래의 명령어로 Elasticsearch index가 존재하는 지 확인할 수 있다.

```shell
curl -XGET 'localhost:9200/_cat/indices?v&pretty'
```

그 결과로 콘솔창에 index가 추가된 것을 확인할 수 있다.

![console]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/apache/elasticsearch-console.png){: .align-center}

## 느낀점

NiFi를 사용할 때는 flowfile의 attribute와 content의 차이를 알고 사용하는게 중요하다. 값을 업데이트하고 싶을때는 content를 attribute로 변환한 후 작업하는 것이 편하다.