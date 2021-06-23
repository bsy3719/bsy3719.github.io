---
title: "[Conda&JupyterLab]] 가상환경 생성 및 커널 생성 "
date: 2021-06-22 21:30:00 -0000

tagline: "by baeksy"
header:
  overlay_image: /assets/images/splash/programer.jpeg
  overlay_filter: 0.5
  #show_overlay_excerpt : false

toc: true
toc_label: "conda/jupyter lab"
toc_sticky: true

categories: 
  - conda
  - jupyter lab
tags: 
  - Bigdata
description: 아나콘다로 가상환경을 생성한 후 jupyter lab에 커널을 추가하는 방법을 설명하는 글입니다.
---

### 가상환경 생성

가상환경 생성 후 파이썬과 ipykernel을 설치한다.
```shell
// 가상환경 생성
conda create --n <가상환경이름>
// 가상환경 실행
conda activate <가상환경이름>
// 필요한 버전의 파이썬 설치
conda install python==<파이썬버전>
// 커널 설치
conda install ipykernel
```

### 주피터 커널 추가

아래의 명령어로 JupyterLab에서 그 가상환경을 쓸 수 있도록 추가해준다.

```shell
// 주피터 커널 생성
python -m ipykernel install --user --name <가상환경이름>
```
아래와 같이 커널이 추가된 것을 확인할 수 있다.
 
 ![architecture]({{ site.url }}{{ site.baseurl }}/assets/images/jupyter/jupyter_lab.png){: .align-center}
 
### 주피터 커널 삭제

 ```shell
 // 주피터 커널 리스트 확인
 jupyter kernelsepc list

 // 주피터 커널 삭제
 jupyter kernelspec uninstall <커널이름>

 //가상환경 삭제
 conda remove --name <가상환경이름> --all
 ```