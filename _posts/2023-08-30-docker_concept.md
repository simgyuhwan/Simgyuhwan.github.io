<!-- ---
title: "Docker란?"
layout: post
date: 2023-08-30 13:50
headerImage: false
tags: [Blog, docker]
star: false
category: blog
author: gyuhwan
excerpt: "Docker의 개념"
--- -->

# Docker



## 1. Docker 란?

Docker는 컨테이너 기반 가상화 도구입니다. 리눅스 상에서 컨테이너 방식으로 **프로세스를** **따로** **분리**하여 실행하고 관리 할 수 있게 해줍니다. 이미지를 기반으로 실행되며, 다시 특정 이미지로 저장하여 보관하고 공유할 수 있습니다.

 

### 1) 컨테이너란?

애플리케이션 코드가 라이브러리와 특정 의존성있는 항목들과 패키징되어 있는 소프트웨어 실행 유닛입니다. 프로세스단에 컨테이너를 올리면 설치되어 있는 OS의 CPU, 메모리, 디스크 양을 제어하여 사용합니다. 따라서 경량화 되어 있습니다.

 

## 2. 가상머신과 컨테이너의 차이

간단하게 요약하면 자주 사용하던 VMware나 Virtual Machine과 같은 VM(virtual Machine)은 **하드웨어** **+ OS**를 가상화 하는 것이고 컨테이너는 하드웨어를 뺀 **OS**만 가상화 하는 것이다. 

설치된 호스트 OS와 서비스 운영 환경을 분리하여 운영하는 것이 컨테이너다. 때문에 경량화 되어있고 이식성이 뛰어나다.

 

### 1) VM

![docker_concept1](/assets/images/concept/docker_concept1.png)

Host OS위에 Hyperviser를 설치하고 각각의 Guest OS를 설치하여 운영한다. 하드웨어를 가상화하여 분리하고 그 안에 OS를 설치하기 때문에 상대적으로 용량을 많이 차지하게 되지만 Host OS와 부닐된 공간과 자원을 할당받아 사용되기 때문에 보안성은 높다.

 

## 2) Container

![docker_concept2](/assets/images/concept/docker_concept2.png)

코드와 종속성을 함께 패키징하는 Application 계층의 추상화라고 정의

여러 종류의 컨테이너가 동일한 시스템에서 실행되고 OS 커널을 다른 컨테이너와 공유가 가능합니다. 각기 다른 OS에서 같은 자원을 사용하는 프로세스라고 보면 됩니다.

 

## 3. 이미지란?

**이미지란** **컨테이너** **실행에** **필요한** **파일과** **설정값**들을 나타내고 이것을 **실행한** **상태를** **컨테이너**라고 합니다.

같은 이미지로 여러 개의 컨테이너를 생성할 수 있고 컨테이너를 수정하더라도 이미지에 변동은 없습니다. 이런 기능이 가능한 이유는 **이미지** **레이어**와 **컨테이너** **레이어**로 나누었기 때문입니다.

 

![docker_concept3](/assets/images/concept/docker_concept3.png)

### 1) Image Layer

이미지를 첫 상태를 나타냅니다. 도커의 경우 레이어를 사용하여 관리합니다. 처음 이미지를 실행하면 이미지 레이어까지만 생성됩니다.

 

 

![docker_concept4](/assets/images/concept/docker_concept4.png)

 

### 2) Container Layer

컨테이너 레이어는 Docker를 통해 컨테이너를 생성하여 들어 가게되면 이미지 레이어까지 생성됩니다. 그 후, 필요한 패키지를 설치할 때마다 Container Layer가 하나씩 추가됩니다.

만약 컨테이너 레이어에서 했던 작업을 Image layer에도 적용하고 싶다면 **docker commit** 명령어를 사용하면 됩니다.

  

## 3-1 Docker Build

위에 적어놓은 Container Layer를 docker를 실행하고 직접 명령어를 입력하여 적용할 수도 있지만 Dockerfile을 사용하여 Docker build시 자동적으로 입력되게 할 수 있습니다.

 

**Dockerfile**

![docker_concept5](/assets/images/concept/docker_concept5.png)

Dockerfile에 명령어들을 입력하고 build를 할 경우, 각 명령어에 따라 이미지가 생성됩니다. 이렇게 생성된 이미지가 층을 이루게 되어 layer 형식으로 쌓이게 됩니다.

위와 같이 작성 후, 빌드 시키면 7개의 층이 쌓이게 되는데.

![docker_concept6](/assets/images/concept/docker_concept6.png)

위와 같이 step 7 단계로 생성되는 것을 확인할 수 있습니다.

이와 같이 이미지 레이어를 사용하는 이유는, 중간에 환경이나 버전 또는 소스를 수정할 때 전체 이미지를 다시 다운 받는 것 보다 해당하는 층을 수정하면 보다 효율적으로 바꿀 수 있기 때문입니다.

 

**Docker run** **실행** **과정**

1. 내부에서 먼저 이미지를 Pull 한다. 만약 이미지가 없다면 Docker hub에서 이미지를 검색하고 찾는다.
2. 찾은 이미지를 이용해 컨테이너를 생성한다.
3. Image     Layer를 생성하고 그 위에 Container Layer를 배치한다. 컨테이너는 파일 시스템에 저장되고 readable,     writeable 이미지가 생성된다.
4. 네트워크 통신(bridge/network     interface)을 통해 Docker와 Host OS간 통신을 열어준다.
5. 사용 가능한 Ip 주소를 찾고 세팅 시켜준다.
6. 애플리케이션을 실행시켜 준다.
7. 애플리케이션 실행 시, 보여지는 화면 또는 입력, 에러를 표시해준다.

## 4. Docker 동작 원리

**#docker run -it --rm ubuntu:18.04 /bin/bash**

다음과 같이 명령어를 입력하여 Docker를 실행하면 어떻게 동작할까? 먼저 간단하게 4가지를 알아야 합니다.

- **Docker     Client**
- **Docker     Server**
- **Image     Cache**
- **Docker     Hub**

 

Docker Client란, Docker를 실행한 호스트 OS라고 보면 됩니다. 처음 사용자가 도커를 사용하려는 컴퓨터를 의미합니다.

Docker Server란, 호스트 OS에 올라간 Docker Daemon을 의미합니다.

Image Cache란, 간단하게 도커 이미지라고 생각하면 됩니다. docker 데몬이 로컬에 저장된 이미지를 캐시처럼 사용한다.

Docker Hub는 이미지들이 저장되어 있는 공식 홈페이지라고 생각하면 됩니다.

 

먼저 **#docker run [hello-world]**를 통해 해당 이미지를 컨테이너화 시키는 명령어를 입력합니다. 그러면 Docker Client는 명령을 해석해서 Docker Server(Docker Daemon)에 전달합니다.

 

 ![docker_concept7](/assets/images/concept/docker_concept7.png)

 hello-world 이미지를 실행하라는 명령이므로, Docker server는 이미지들이 저장되어 있는 Image Cache에서 해당 이미지 파일이 있는지 확인 합니다.

 

**Docker Client -> Docker Server -> Image Cache**

 

만약 Image Cache를 확인을 했으나 파일이 존재하지 않으면 도커 허브에 접속하여 'hello-world' 라는 이미지가 존재하는지 검색하고 Image Cache에 다운로드 합니다.

 

 ![docker_concept8](/assets/images/concept/docker_concept8.png)
 

이렇게 Image Cache에 다운로드한 이미지를 컨테이너화하여 사용하게 됩니다.

 

**참고 및 출처** 

- [도커 입문편](https://www.44bits.io/ko/post/easy-deploy-with-docker#도커-이미지docker-image-기초)
- [초보를 위한 도커 안내서](https://subicura.com/2017/02/10/docker-guide-for-beginners-create-image-and-deploy.html)
- [Docker Client/Server](https://macro.tistory.com/entry/Docker-Client-Server-Docker-Hub-관계-docker-run-명령)