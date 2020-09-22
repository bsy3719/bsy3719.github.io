---
title: "[NiFi] Apache NiFi 소개"
date: 2020-09-15 20:00:00 -0000

tagline: "by baeksy"
header:
  overlay_image: /assets/images/splash/nifi.png
  overlay_filter: 0.5
  #show_overlay_excerpt : false

toc: true
toc_label: "Apache NiFi 소개"
toc_sticky: true

categories: 
  - bigdata
tags: 
  - NiFi
description: Apache NiFi에 대해 소개하는 글입니다.
---
아파치 나이파이(Apache NiFi, 나이아가라파일즈/NiagaraFiles의 준말)는 소프트웨어 시스템 간 데이터 흐름을 자동화하도록 설계된 아파치 소프트웨어 재단의 프로젝트이다. 과거에 NSA에 의해 개발되었고 2014년 기술 전송 프로그램의 일부로서 오픈소스화된 나이아가라파일 소프트웨어에 기반을 둔다.

- Dataflow를 쉽게 개발할 수 있고, 시스템 간의 데이터 이동과 내용을 볼 수 있는 기능과 UI 제공
- 실시간 데이터 전송에 필요한 유용한 기능을 제공
- 강력한 자원과 권한 관리를 통한 멀티테넌시(Multi-tenant, 여러 조지깅 자원을 공유해서 사용)를 지원
- 데이터 어느 시스템으로부터 왔는지 추적할 수 있음
- 오픈소스이면서, 호튼웍스의 기술 지원을 받을 수 있음
- 여러 NiFi 시스템 간 통신을 지원함(Site-to-Site)

NiFi는 Flow Based Programming(FBP)의 개념을 구현했으며, 프로세서를 이용하여 수집, 변형, 저장을 여러 단계에 거쳐서 할 수 있다. 다음은 NiFi에서 사용하는 주요 용어들이다.

| NiFi용어           | FBP용어            | 설명                                                         |
| ------------------ | ------------------ | ------------------------------------------------------------ |
| FlowFile           | Information Packet | NiFi에서 데이터를 표한하는 객체로, Key/Value 형태의 데이터 속성(Attribute)과 데이터(Content)를 포함할 수 있다. 데이터는 0바이트 이상의 데이터가 저장될 수 있다. FlowFile을 이용하여 여러 시스템 간의 데이터 이동이 가능하다. |
| FlowFile Processor | Black Box          | FlowFile은 여러 단계에 걸쳐 속성이 추가되거나 내용이 변경될 수 있는데, 이때 사용되는 것이 FlowFile Processor이다. Nifi는 150개 이상의 Processor를 제공하는데, 이를 이용하여 FlowFile을 다양한 시스템으로부터 읽어와 변경, 저장을 할 수 있다. |
| Connection         | Bounded Buffer     | Processor간의 연결을 말하며, NiFi의 Connetion은 FlowFile의 대기열(queueing)뿐만 아니라 라우팅, 처리량 제한, 우선순위 제어, 모니터링 등의 강력한 기능을 제공한다. |
| Flow Controller    | Scheduler          | Controller는 프로세스가 모든 프로세스가 사용하는 스레드 및 할당을 연결하고 관리하는 방법을 관리한다.  플로우 컨트롤러는 프로세서 간의 플로우 파일 교환을 용이하게하는 브로커 역할을 한다. |
| Process Group      | subnet             | 특정 업무, 기능 단위로 여러 Processor를 묶을 수 있으며, Input과 Output 포트를 제공해 Process Group간의 데이터 이동이 가능하다. |

## 장단점

장점

- 실시간 처리를 잘함
- 장애시 복구될때가지 데이터 처리를 못할 수 있지만, 잃어버리는 일은 없음
- Zero Master 클러스터 환경을 제공하고, 쉽게 확장 가능
- HTTPS를 지원해 통신에 대한 보안
- 웹 기반에 비교적 직관적인 인터페이스를 제공함으로써 사용하기 쉬움
- 데이터의 이동 경로를 추적 가능
- 클러스터간의 Site-to-Site를 이용해 데이터 교환 가능

단점

- 배치작업을 잘 못함(하둡 환경에서 대용량 파일을 빠르게 전송해야한다면 DistCp가 더 좋음)
- 원본 소스 파일들이 목적지까지 다 전달되었는지 알기 어려움
- 오직 실행이 끝나고 성공과 실패, 출력 결과만을 알 수 있음
- 간단한 데이터 조작만 가능하지, 복잡한 연산은 잘 못함(Spark, Storm과 연동 가능)

## 아키텍처

![architecture]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/intro/architecture.png){: .align-center}

NiFi는 JVM 환경에서 실행되며, 위 그림과 같은 컴포넌트들로 구성되어 있다,

### Web Server

NiFi는 UI를 웹 서비스를 통해 제공하며, 개발자 혹은 관리자는 이를 이용하여 Dataflow를 개발, 제어, 모니터링을 한다.

### Flow Controller

Processor(Extension)가 실행되도록 쓰레드를 만들고, 프로세서를 실행하는 스케줄링 담당

### Extension

NiFI가 제공하는 기본 Processor들 외, 개발자가 프로세스를 개발해 확장할 수 있다.

### FlowFile Repository

Write-Ahead-Log로 FlowFile의 상태와 속성값들을 저장하는 곳이다. 일반적으로 Raid 10으로 디스크를 구성하여 저장해, 시스템 장애때 유실되지 않게 한다.

### Content Repository

FlowFile의 데이터(Content)가 저장되며, 일반적으로 Raid10으로 디스크를 구성해 저장하며, 여러 디렉토리에 분석 저징이 가능하다. 이 때문에 용량이 큰 데이터를 저장할 수 있으며, 단일 디스크의 처리량보다 많은 양을 처리할 수 있다. NiFi 시스템의 역할을 봤을 때, 일반적으로 여러 파티션을 사용할 경우가 많지 않다.

### Provenance Repository

데이터의 처리 단계별로 FlowFile의 변화데이터를 보관하는 곳으로 여러 디스크를 지원하며, 각 데이터는 인덱스 되어 검색할 수 있다.

## 나이파이 설치(MAC)

### brew를 이용한 설치

> NiFi를 사용하기 전에 java가 설치되어 있어야함(현재 MAC에 1.8.0_181버전 설치)

```shell
brew install nifi
```

### NiFi 실행

```shell
nifi start
```

### NiFi 상태 확인

```shell
nifi status
```

### NiFi 종료

```shell
nifi stop
```

## 나이파이 설치(Linux)

### NiFi 설치

> NiFi를 사용하기 전에 java가 설치되어 있어야함(리눅스에 1.8.0_181버전 설치)

```shell
wget https://downloads.apache.org/nifi/1.11.4/nifi-1.11.4-bin.tar.gz
# 다운로드 폴더 위치
tar zxfv nifi-1.11.4-bin.tar.gz
```

- NiFi 자바 경로 설정

```shell
vi ./bin/nifi-env.sh
```

- jdk 경로로 변경

```shell
export JAVA_HOME=[자바 경로]
```

### NiFi 실행

```shell
./bin/nifi.sh start
```

### NiFi 상태 확인

```shell
./bin/nifi.sh status
```

### NiFi 종료

```shell
./bin/nifi.sh stop
```

## NiFi 웹 구성 요소

NiFi 실행 후 Web브라우저를 열고 localhost:8080/nifi를 입력한다. 기본 포트로 8080이 설정되어 있지만, conf/nifi.porperties 수정이 가능하다.

![툴바-컴포넌트]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/intro/toolbar-components.png){: .align-center}

**Componets Toolbar**

NiFi에서 사용하는 컴포넌트들이다. 이 컴포넌트를 클리갛여 드래그&드롭으로 캔버스에 컨포넌트를 등록시킬 수 있다.

**Global Menu**

![global-menu]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/intro/global-menu.png){: .align-center}


- Summary : NiFi에 등록된 컴포넌트들을 종합적으로 보고, 검색할 수 있다.
- Counters : Mapreduce의 카운터와 유사한 기능으로 특정 Processor에서 발생시키는 카운트 정보를 제공한다.
- Bulletin Board : 시스템의 문제 등을 볼 수 있다.
- Data Provenace : 데이터를 추적할 수 있다.
- Flow Configuration History : FlowFilw의 들록, 삭제, 변경 등의 이력을 제공한다.
- Users, Policies : 사용자 및 권한을 관리한다. NiFi는 SSL 인증서 기반의 인증(X.509)를 제공하는데, 인증 시스템이 활성화된 경우에만 메뉴가 보인다.
- Templates : Processor와 그 들의 연결 정보인 Connetion 컴포넌트를 속성가지도 유지한 채 템프릸화 할수 있는데, 이렇게 등록된 템플릿을 조회하고, 내려받을 수 있는 기능을 제공한다.
- Help : 도움말을 제공한다.
- About : NiFi 버전을 제공한다.

**Status Bar**

NiFi의 현재 상황을 볼 수 있다. 실행되고 있는 테스크, 프로세스 개수 정보와 오류 정보, 클러스터 노드 정보 등을 제공한다.

**Search**

NiFi에 등록된 Processor, Connection을 검색 할 수 있다.

**Operate Platte**

NiFi 컴포넌트드르이 설정, 활성/비활성화, 시작/멈춤, 템플릿 생성/등록, 컴포넌트 복사/붙여넣기, Processor Group화, 컴포넌트 색 변경, 컴포넌트 삭제 기능을 제공한다. 캔버스에서 컨포넌트를 선택하면, 상황에 따라 버튼들이 비활성화된다.

## NiFi Processor 등록 및 연결

Processor 등록을 위해서는 Compoent Toolbar의 Processor 아이콘을 선택하여, 드래그하여 캔버스의 원하는 위치에 놔두면 아래와 같이 추가할 Processor를 검색할 수 있다.

![add-processor]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/intro/add-processor.png){: .align-center}

오른쪽 상단의 필터에 검색어를 입력하여 태그 또는 프로세서 명으로 필터링 할 수 있으며, 옆 Tag Cloud를 통해 Processor들을 분류할 수도 있다. 사용할 Porocessor를 테이블에서 선택하고, ADD 버튼을 눌러 등록한다. 여기서는 임의의 FlowFile을 생성하는 GenerateFlowFile을 등록한다.

등록된 GenerateFlowFile Processor를 선태갛고, 오른쪽 마우스 -> Configuration을 선택하거나, Operation Palette의 설정 버튼을 눌러 다음과 같은 설정을 할 수 있으며, APPLY를 눌러 설정을 저장할 수 있다. 

![processor-connetction]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/intro/processor-connection.png){: .align-center}

같은 방법으로 LogAttribute라는 Processor도 등록한다. 이는 FlowFile 정보를 NiFi로그 파일 ($NIFI_HOME/logs/nifi-app.log)에 출력시킬 수 있다.

Processor 위에 마우스를 올리면 연결 버튼이 나오는데 이 버튼을 선택하여, GenerateFlowFile과 LogAttribute Processor를 연결하고, Operate Plaette에 시작 버튼을 선택하여 Dataflow를 시작한다. 로그 파일을 "tail -f"로 보면 FlowFile의 속성 정보들이 로그에 출력되는 것을 확인 할 수 있다. 정지 버튼을 선택하여 Dataflow를 정지시킨다.

![basic-processors]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/intro/basic-processors.png){: .align-center}

## 프로세서로 가능한 역할

### Data Transformation

- **CompressContent** : Content 압축 및 압축 해제
- **ConvertCharacterSet** : Character set 변환
- **EncryptContent** : Content 암호화 및 복호화
- **ReplaceText** : 정규표현식을 이용한 텍스트 내용 수정
- **TransformXml** : XML 내용에 XSLT 변환 적용
- **JoltTransformJSON** : JSON Content 변환

### Routing and Mediation

- **ControlRate** : 데이터 흐름 속도 조절
- **DetectDuplicate** : 중복 FlowFile 모니터링
- **DistributeLoad** : 데이터 일부를 사용자 정의 관계에 의해 분배
- **MonitorActivity** : 특정 지점에 데이터가 전송되지 않고 일정 시간이 경과하면 알람을 줌
- **RouteOnAttribute** : FlowFile의 Attribute에 따른 라우팅
- **ScanAttribute** : 사용자 정의 Attribute에 일치하는 지 확인
- **RouteOnContent** : FlowFile의 Content에 따른 라우팅
- **ScanContent** : 사용자 정의 Content에 일치하는 지 확인
- **ValidateXml** : 사용자 XML 스키마에 따라 FlowFile의 내용이 유효한지 검증

### Database Access

- **ConvertJSONToSQL** : JSON을 통한 SQL INSERT, UPDATE
- **ExecuteSQL** : Avro 형식의 FlowFile의 결과를 기록하여 SELECT 명령 실행
- **PutSQL** : FlowFile의 내용으로 정의된 SQL DDM문을 실행하여 데이터베이스를 업데이트
- **SelectHiveQL** : 사용자 정의 HiveQL SELECT 명령을 실행하여 Avro 또는 CSV형식으로 FlowFile에 기록
- **PutHiveQL** : FlowFile의 내용으로 정의된 HiveQL DDM문을 실행하여 Hive 데이터베이스를 업데이트

### Attribute Extraction

- **EvaluateJsonPath** : 
- **EvaluateXPath** : 사용자는 XPath 표현식을 제공 한 다음 이러한 표현식을 XML 컨텐츠와 비교하여 평가하여 FlowFile 컨텐츠를 바꾸거나 사용자 이름 속성으로 값을 추출
- **EvaluateXQuery** :
- **ExtractText** : 정규표현식을 이용하여 FlowFile의 텍스트 내용을 추출
- **HashAttribute** : 기존 Attribute 목록을 연결하여 hashing기능을 수행
- **HashContent** : FlowFile의 내용에 대해 해싱 함수를 수행하고 해시 값을 속성으로 추가
- **IdentifyMimeType** : FlowFile이 캡슐화하는 파일 형식을 결정하기 위해 FlowFile의 내용을 평가. 이 프로세서는 이미지, 워드 프로세서 문서, 텍스트 및 압축 형식과 같은 다양한 MIME 유형을 감지 할
- **UpdateAttribute** : 원하는 수의 사용자 정의 속성을 FlowFile에 추가하거나 업데이트 이는 정적으로 구성된 값을 추가하고 표현식 언어를 사용하여 속성 값을 동적으로 도출하는 데 유용

### System Interaction

- **ExecuteProcess** : 사용자 정의 운영 체제 명령을 실행
- **ExecuteStreamCommand** : 사용자 정의 운영 체제 명령을 실행. FlowFile의 내용은 선택적으로 프로세스의 StdIn으로 스트리밍됩니다. StdOut에 작성된 컨텐츠는 아웃 바운드 FlowFile의 컨텐츠가 됨

### Data Ingestion

- **GetFile** : 파일의 내용을 로컬 디스크 (또는 네트워크 연결 디스크)에서 NiFi로 스트리밍 한 다음 원본 파일을 삭제
- **GetFTP** : FTP를 통해 원격 파일의 내용을 NiFi로 다운로드 한 다음 원본 파일을 삭제
- **GetSFTP** : SFTP를 통해 원격 파일의 내용을 NiFi로 다운로드 한 다음 원본 파일을 삭제
- **GetJMSQueue** : JMS 큐에서 메시지를 다운로드하고 JMS 메시지의 내용에 따라 FlowFile을 작성
- **GetJMSTopic** : JMS 토픽에서 메시지를 다운로드하고 JMS 메시지의 내용에 따라 FlowFile을 작성
- **GetHTTP** : 원격 HTTP 또는 HTTPS 기반 URL의 내용을 NiFi로 다운로드
- **ListenHTTP** : HTTP (또는 HTTPS) 서버를 시작하고 들어오는 연결을 수신. 들어오는 POST 요청의 경우 요청 내용이 FlowFile로 기록되고 200 응답이 반환
- **ListenUDP** : 들어오는 UDP 패킷을 수신하고 패킷 또는 패킷 번들 (구성에 따라 다름)마다 FlowFile을 만들고 '성공'관계로 FlowFile을 내 보냄
- **GetHDFS** : HDFS에서 사용자 지정 디렉토리를 모니터링. 새 파일이 HDFS에 들어갈 때마다 파일이 NiFi로 복사되고 HDFS에서 삭제
- **ListHDFS** / **FetchHDFS** : ListHDFS는 HDFS에서 사용자 지정 디렉토리를 모니터링하고 발견 된 각 파일의 파일 이름이 포함 된 FlowFile을 생성. 그런 다음 이러한 FlowFile을 클러스터에서 팬 아웃하여 FetchHDFS 프로세서로 전송할 수 있음. FetchHDFS 프로세서는 해당 파일의 실제 컨텐츠를 가져오고 HDFS에서 가져온 컨텐츠를 포함하는 FlowFile을 생성
- **FetchS3Object** : AWS (Amazon Web Services) Simple Storage Service (S3)에서 객체의 내용을 가져옵니다.
- **GetKafka** : Apache Kafka, 특히 0.8.x 버전의 메시지를 가져옵니다. 메시지는 메시지 당 FlowFile로 생성되거나 사용자 지정 구분 기호를 사용하여 함께 일괄 처리 될 수 있음
- **GetMongo** : MongoDB에 대해 사용자 지정 쿼리를 실행하고 내용을 새 FlowFile에 씁니다.
- **GetTwitter** : 사용자가 Twitter "Garden hose" 또는 Enterprise 엔드 포인트를 청취하도록 필터를 등록하여 수신되는 각 트윗에 대한 FlowFile을 작성할 수 있음

### Data Egress / Sending Data

- **PutEmail** : 구성된 수신자에게 이메일을 보냅니다. FlowFile의 내용은 선택적으로 첨부 파일로 전송됩니다.
- **PutFile** : FlowFile의 내용을 로컬 (또는 네트워크 연결) 파일 시스템의 디렉토리에 씁니다.
- **PutFTP** : FlowFile의 내용을 원격 FTP 서버로 복사합니다.
- **PutSFTP** : FlowFile의 내용을 원격 SFTP 서버에 복사합니다.
- **PutJMS** : FlowFile의 내용을 JMS 메시지로 JMS 브로커에 전송하고 선택적으로 속성을 기반으로 JMS 특성을 추가
- **PutSQL** : FlowFile의 내용을 SQL DDL 문 (INSERT, UPDATE 또는 DELETE)으로 실행
- **PutKafka** : FlowFile의 내용을 Apache Kafka, 특히 0.8.x 버전의 메시지로 메시지로 보냄
- **PutMongo** : FlowFile의 내용을 INSERT 또는 UPDATE로 Mongo에 보냄

### Splitting and Aggregation

- **SplitText** : SplitText는 내용이 텍스트 인 단일 FlowFile을 가져와 구성된 줄 수에 따라 하나 이상의 FlowFile로 분할
- **SplitJson** : 사용자가 배열 또는 많은 하위 객체로 구성된 JSON 객체를 JSON 요소 당 FlowFile로 분할
- **SplitXml** : 사용자가 XML 메시지를 각각 원본의 세그먼트를 포함하는 많은 FlowFile로 분할
- **UnpackContent** : ZIP 및 TAR과 같은 다양한 유형의 아카이브 형식을 압축 해제
- **MergeContent** :이 프로세서는 많은 FlowFile을 단일 FlowFile로 병합합니다. 선택적 헤더, 바닥 글 및 구분 기호와 함께 컨텐츠를 연결하거나 ZIP 또는 TAR과 같은 아카이브 형식을 지정하여 FlowFile을 병합 할 수 있음
- **SegmentContent** : 일부 구성된 데이터 크기에 따라 FlowFile을 잠재적으로 여러 개의 작은 FlowFile로 분할. 분할은 모든 종류의 구분자에 대해 수행되는 것이 아니라 바이트 오프셋에 기초하여 수행
- **SplitContent** : 단일 FlowFile을 SegmentContent와 유사하게 잠재적 인 많은 FlowFile로 분할

### HTTP

- **GetHTTP** : 원격 HTTP 또는 HTTPS 기반 URL의 내용을 NiFi로 다운로드
- **ListenHTTP** : HTTP (또는 HTTPS) 서버를 시작하고 들어오는 연결을 수신합니다. 들어오는 POST 요청의 경우 요청 내용이 FlowFile로 기록되고 200 응답이 반환
- **InvokeHTTP** : 사용자가 구성한 HTTP 요청을 수행합니다. 이 프로세서는 GetHTTP 및 PostHTTP보다 훨씬 다재다능하지만 조금 더 구성이 필요함
- **PostHTTP** : FlowFile의 내용을 메시지 본문으로 전송하여 HTTP POST 요청을 수행합니다. Site-to-Site를 사용할 수없는 경우 서로 다른 두 개의 NiFi 인스턴스간에 데이터를 전송하기 위해 ListenHTTP와 함께 사용
- **HandleHttpRequest** / **HandleHttpResponse** : HandleHttpRequest 프로세서는 ListenHTTP와 유사하게 내장 HTTP (S) 서버를 시작하는 소스 프로세서입니다. 그러나 클라이언트에 응답을 보내지 않습니다. 대신, FlowFile은 HTTP 요청의 본문과 함께 모든 일반 서블릿 매개 변수, 헤더 등에 대한 속성 및 속성으로 속성으로 전송됩니다. 그러면 HandleHttpResponse는 FlowFile 처리가 완료된 후 클라이언트로 응답을 다시 보낼 수 있습니다. 이 프로세서는 항상 서로 함께 사용되어야하며 사용자는 NiFi 내에서 웹 서비스를 시각적으로 만들 수 있습니다.

### Amazon Web Services

- **FetchS3Object** : Amazon Simple Storage Service (S3)에 저장된 객체의 컨텐츠를 가져옵니다. S3에서 검색된 컨텐츠는 FlowFile의 컨텐츠에 기록됩니다.
- **PutS3Object** : 구성된 자격 증명, 키 및 버킷 이름을 사용하여 FlowFile의 내용을 Amazon S3 객체에 사용
- **PutSNS** : FlowFile의 내용을 Amazon Simple Notification Service (SNS)에 알림으로 보냄
- **GetSQS** : Amazon Simple Queuing Service (SQS)에서 메시지를 가져 와서 메시지 내용을 FlowFile의 내용에 사용
- **PutSQS** : FlowFile의 내용을 Amazon Simple Queuing Service (SQS)에 메시지로 보냄
- **DeleteSQS** : Amazon Simple Queuing Service (SQS)에서 메시지를 삭제합니다. SQS에서 메시지를 받기 위해 GetSQS와 함께 사용할 수 있음





