## NiFi -Apache Access Log

---

### Apache Access Log 생성

Github에서 Apache Access Log를 생성하는 파일을 다운받는다.

https://github.com/kiritbasu/Fake-Apache-Log-Generator

**기본적인 사용법**

- 100개 샘플 로그를 10초 간격으로 생성

  ```shell
  python apache-fake-log-gen.py -n 100 -s 10 -o LOG 
  ```

- -n : 샘플 로그의 갯수(0을 입력히 무한히 생성)
- -s : 라인을 생성하는 주기
- -o : output file의 타입

### NiFi Processor

**전체 화면**

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710154206035.png" width="500" height="400">

**TailFile**

생성된 로그를 한줄씩 읽어오는 Processor이다.

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710135055614.png" height="400">

- Tailing mode : Single file / Multiple files 옵션을 선택
- File(s) to Tail : 읽어올 파일의 위치,  Single file일 경우 전체 경로를 입력
- Base directory : Multiple files의 경우 기본 경로를 입력, Single file의 경우 입력할 필요 없음

**ExtractGrok**

읽어온 log를 Grok Expression으로 파싱해주는 Processor

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710143802763.png" height="400">

- Grok Expression : grok expresson으로 파싱할 내용을 작성 (grok expression에서는 자주 사용하는 기본형을 제공)

  참조 - https://nifi.apache.org/docs/nifi-docs/components/org.apache.nifi/nifi-record-serialization-services-nar/1.5.0/org.apache.nifi.grok.GrokReader/additionalDetails.html

Grok Expression으로 FlowFile의 Content를 파싱하면 Attribute들은 아래와 같이 바뀐다.

파싱 전 FlowFile의 Attribute

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710144831303.png" height="400">

파싱 후 FlowFile의 Attribute

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710145138158.png" height="400">

**UpdateAttribute**

FlowFile의 Attribute를 업데이트 해주는 Processor, 생성할 Attribute는 우측 상단의 Add Property버튼을 클릭하여 작성한다.

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710144413633.png" height="400">

- Delete Attributes Expression : 정규표현식을 이용하여 삭제할 Attribute를 지정

이후 필요한 내용의 Attirbute들을 추가, 수정, 삭제하면 된다.

업데이트 후 FlowFile의 Attribute

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710145330517.png" height="400">

**AttributesToJSON**

FolwFile의 Attribute를 JSON으로 변환해주는 Processor

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710144424496.png" height="400">

- Destination : flowfile-attribute, flowfile-content 옵션이 존재하여 변한된 json을 attibute에 저장할 것인지, content에 저장할 것인지를 설정

변환 전 FlowFile의 Content

157.239.156.249 - - [10/Jul/2020:14:24:29 +0900] "GET /explore HTTP/1.0" 200 4976 "http://blair.com/author/" "Mozilla/5.0 (iPhone; CPU iPhone OS 9_3_6 like Mac OS X) AppleWebKit/531.1 (KHTML, like Gecko) CriOS/17.0.860.0 Mobile/58W977 Safari/531.1"

변환 후 FlowFile의 Content

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

**PutElasticsearchHttp**

JSON으로 변환된 FlowFile의 Content를 Elasticsearch에 전송해주는 Processor

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710144433791.png" height="400">

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710150107529.png" height="400">

- Elasticsearch URL : 연동할 Elasticsearch의 URL

- index : Elasticseach로 연동될 데이터의 index

  NiFi Expression Language : NiFi에서 Attribute를 조작하는 기능을 제공

  참조 - https://nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html#strings

- Type : Elasticseach로 연동될 데이터의 type

**LogAttribute**

FlowFile 정보를 NiFi로그 파일 ($NIFI_HOME/logs/nifi-app.log)에 출력해주는 Processor

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710144441766.png" height="400">

**Connection 연결시 주의사항**

Processor를 실행시킬 때 해당 Processor의 Connection이 모두 연결되어 있지 않은 상태에서는 아래의 그림과 같이 오류를 발생하게 된다. 

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710150544709.png" height="400">

이를 해결하기위해서는 연결되지 않는 Connetion을 Automatically Terminate Relationships에 체크를 해주어야 한다.

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710150616206.png" height="400">

**Queue**

Queue를 클릭하게 되면 아래와 같은 창이 나타난다.

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710150924488.png" height="400">

Configure에서는 queue의 이름, 우선순위 등을 설정 할 수 있다.

<img src="/Users/baek/Library/Application Support/typora-user-images/image-20200710151016877.png" height="400">

- FlowFile Expiration : object가 flow에서 존재할 수 있는 최대 시간
- Back Pressure Object Threshold : queue에 존재할 수 있는 object의 수
- Size Threshold : queue의 최대 data size
- Available Prioritizers : queue에서 처리할 우선순위
- Load Balance Strategy : 이 connection의 데이터를 클러스터의 노드에서 load balance하는 방법

List Queue를 클릭하면 해당 queue에 존재하는 FlowFile의 Attribute와 Content를 확인할 수 있다.

![image-20200710151855258](/Users/baek/Library/Application Support/typora-user-images/image-20200710151855258.png)

### Elasticsearch 확인

Elasticsearch index가 존재하는 지 확인

```shell
curl -XGET 'localhost:9200/_cat/indices?v&pretty'
```

아래와 같이 콘솔창에서 확인할 수 있다.

![image-20200710152953383](/Users/baek/Library/Application Support/typora-user-images/image-20200710152953383.png)