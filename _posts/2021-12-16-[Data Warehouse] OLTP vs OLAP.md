---
title: "[Apache Spark] RDD"
date: 2021-12-16 20:00:00 -0000

tagline: "by baeksy"
header:
  overlay_image: /assets/images/splash/programer.jpeg
  overlay_filter: 0.5
  #show_overlay_excerpt : false

toc: true
toc_label: "Database"
toc_sticky: true

categories: 
  - Database
tags: 
  - Data Warehouse
description: OLTP와 OLAP의 특징을 설명하고 비교한 글입니다.
---

### OLTP(Online Transaction Processing)

- 온라인 트랜잭션 처리
- 네트워크 상의 여러 사용자가 실시간으로 데이터베이스를 갱신하거나 조회하는 등의 단위 작업을 처리하는 방식
- 송수신 자료를 트랜잭션 단위로 압축했기에 기존 컴퓨터 통신에서 사용해 왔던 온라인 방식과는 달리, 다수의 이용자가 거의 동시에 이용가능
- ex) 한개의 요청작업을 처리하는 과정(은행에 돈을 입금하고 그 처리 결과를 처리)
- 1개의 트랜잭션에서 발생되는 insert, update, delete의 과정을 무결성을 보장하여 처리하고 그 결과를 select하는 과정

### OLAP(Online Analytical Processing)

- 온라인 분석 처리
- 사용자가 다양한 각도에서 직접 대화식으로 정보를 처리하는 과정
- 단독으로 존재하는 정보시스템이 아니라, 데이터 웨어하우스나 데이터 마트와 같은 시스템에 상호연관
- 데이터를 분석하고 의미있는 정보를 치환하거나, 복잡한 모델리을 가능하게끔 하는 분석 방법을 의미
- OLAP는 기존에 저정되어있는 데이터를 사용자의 요구와 목적에 맞게 분석하여 정보를 제공하는 개념을 의미

### OLTP와 OLAP의 차이점
- OLTP는 한개의 데이터 처리가 얼마나 정확하고, 무결한지가 중요
- 그렇기 때문에 데이터의 저장, 삭제, 수정 등의 실릭적인 데이터를 수정하는 작업을 의미하는 용어
- OLAP는 이미 저장된 데이터를 바탕으로 어떤 정보를 제공하는지가 중요
- 따라서 OLAP는 데이터가 무결하고 정확하다는 전제를 바탕으로 고객 또는 사용자가 원하는 정보를 어떤 식으로 표한하고 저장하는 지를 의미

![Data Warehouse]({{ site.url }}{{ site.baseurl }}/assets/images/dw/OLTP_vs_OLAP.png){: .align-center}


