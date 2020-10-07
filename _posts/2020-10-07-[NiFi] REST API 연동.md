---
title: "[NiFi] REST API 연동"
date: 2020-10-07 00:00:00 -0000

tagline: "by baeksy"
header:
  overlay_image: /assets/images/splash/nifi.png
  overlay_filter: 0.5
  #show_overlay_excerpt : false

toc: true
toc_label: "NiFi REST API 연동"
toc_sticky: true

categories: 
  - bigdata
tags: 
  - NiFi
description: Apache NiFi를 이용하여 API 연동하는 방법을 설명한 글입니다.
---

이 글은 NiFi를 활용하여 GET방식으로 API를 호출하는 방법을 설명하는 글이다. 전체적인 흐름은 FlowFile 생성 -> API 호출 -> JSON 분할 -> 로그 쌓기 순으로 진행된다. 이 글은 API 호출하는 방법에 대해 설명하므로 JSON형태의 데이터는 이후 필요에 따라 가공하여 사용하면 된다.

## OPEN API 테스트

REST API 테스트를 하기 위해서 [공공데이터포털](https://www.data.go.kr/)의 지상(종관, ASOS) 시간자료 조회서비스 신청해서 테스트했다. 공공데이터포털에 가입하여 기상청으로 조회를 하면 많은 OPEN API를 제공한다. 그 중 하나를 선택하여 테스트를 진행하면 된다. 신청 후 마이페이지로 이동하면 내가 선청한 OPEN API 내역을 볼 수 있다.

![api-list]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/api/api-list.png){: .align-center}

신청한 API를 선택하여 참고문서를 연다. 참고 문서에 친절하게 요청메시지 샘플이 있다. 샘플에 키값만 바꾸어 그대로 사용했다.

![sample-request]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/api/sample-request.png){: .align-center}


## 전체 흐름

![total-view]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/api/total-view.png){: .align-center}

- GenerateFlowFile : 사용자 정의의 flowfile을 생성하는 프로세서
- InvokeHTTP : HTTP 메서드를 이용해 HTTP 엔드포인트와 상호작용할 수 있도록 해주는 프로세서
- SplitJson : jsonPath식으로 배열을 하나의 json으로 분할하여 개별의 flowfile을 생성하는 프로세서
- LogAttribute : 지정된 경로에 로그파일을 생성하는 프로세서. 테스트를 위해서 사용

## 프로세서 설명

### GenerateFlowFile

![GenerateFlowFile]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/api/GenerateFlowFile.png){: .align-center}

- servicekey : flowfile을 생성할때 API키를 attribute로 생성한다.

### InvokeHTTP

![InvokeHTTP]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/api/InvokeHTTP.png){: .align-center}

- HTTP Method : 요청할 HTTP method를 작성(GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS), PUT, POST, PATCH 이외의 method들은 message body없이 전송한다.
- Remote URL : 연동할 URL, API문서의 sample을 그대로 사용하였다.

### SplitJson

![SplitJson]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/api/SplitJson.png){: .align-center}

- JsonPath Expression : JsonPath를 이용하여 split할 json을 찾는다.

## 결과

아래는 SplitJson으로 내용을 분할한 결과이다. jsonpath를 이용하여 response -> body -> items -> item에 존재하는 json배열을 추출하여 분할한다. 이와 같은 방법으로 원하는 json의 값을 추출할 수 있다.

### 분할 전

![split-before]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/api/split-before.png){: .align-center}

### 분할 후

![split-after]({{ site.url }}{{ site.baseurl }}/assets/images/nifi/api/split-after.png){: .align-center}

## 느낀점

일을 하면서 API 호출 -> json 파싱 -> Easticsearch에 저장하는 로직을 만들었다. 데이터를 json형태로 받으면 파싱하기도 편하고 많은 곳에서 input 데이터로 활용할 수 있다. 이번 예제에서는 다루지 않았지만 POST method의 경우 flowfile의 content에 요청할 파라미터들을 json으로 만들어 InvokeHTTP 프로세서를 활용하여 보내면 된다.