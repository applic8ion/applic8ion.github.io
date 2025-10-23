---
layout: post
title: "[Docker] Colima 기본 사용법"
categories: [Docker]
tags: [Docker]
date: 2025-10-23 19:50 +0900
image:
  path: /assets/img/2025-10-23-docker-for-macos/favicon.jpeg
---

## Docker 개념

애플리케이션과 그 실행 환경(라이브러리, 설정 등)을 컨테이너 단위로 패키징, 배포, 실행할 수 있게하는 오픈소스 가상화 플랫폼

## macOS에서 Docker 사용하는 방법 비교

<details>
<summary>1. Docker Desktop (공식 GUI 올인원)</summary>
<div markdown="1">

- 구성
  - macOS용 데스크톱 앱 + 자체 경량 VM(Apple Virtualization/HyperKit)
  - Docker Engine, Buildx, Compose v2, (옵션) Kubernetes 내장
  - GUI로 이미지/컨테이너/리소스 관리
- 장점
  - 설치·초기 설정이 가장 쉽고 빠름 (GUI 친화)
  - 기본으로 Buildx/Compose 동작, 설정 손이 덜 감
  - 파일 공유·포트 포워딩, 인증 등 대부분 자동
  - 개인 사용은 무료(기업 규모 제한 있음 → 개인 PC 사용은 OK)
- 단점
  - 백그라운드 리소스 사용이 비교적 큼(팬/배터리)
  - 자동화·헤드리스(스크립트 중심) 워크플로에는 상대적으로 덜 맞음
  - 서버(Ubuntu)와 구조 차이(툴 체인/경로)가 있어 감각적으로는 살짝 이질감
- 언제 적합?
  - “GUI로 빠르게 시작하고 싶다”, “학습/개인 프로젝트”, “쿠버네티스 미니클러스터까지 한 번에”
  - 회사 라이선스 정책상 사용 가능하고, 손쉬움이 최우선일 때

</div>
</details>

<details>
<summary>2. Colima + Docker CLI (오픈소스 경량 조합)</summary>
<div markdown="1">

- 구성
  - Colima가 Lima 기반 리눅스 VM을 띄우고 그 안에 Docker Engine을 자동 설치
  - 로컬 docker CLI가 Colima VM의 엔진에 연결되어 동작
- 장점
  - 가볍고 단순(필요할 때만 colima start, 끝나면 colima stop)
  - 서버(Ubuntu)와 더 비슷한 구조(헤드리스·스크립트·CI/CD 친화)
  - 100% 오픈소스, 기업/개인 라이선스 제약 거의 없음
  - 멀티 프로필로 다양한 VM 환경 분리 가능(--profile)
- 단점
  - GUI가 없고, 컨텍스트/프로필 개념 등 초반 학습 필요
  - Compose 플러그인 경로 설정 등 약간의 수동 세팅이 생길 수 있음
  - Kubernetes는 별도(Colima의 k8s 옵션 또는 kind/k3d 등)로 구성
- 언제 적합?
  - “개발 → 서버 배포”가 스크립트 기반이고 CI/CD를 적극 활용할 때
  - 기업/팀 표준이 Docker Desktop이 아니거나, 경량·자동화를 선호할 때
  - 서버와 최대한 유사한 환경을 로컬에서 유지하고 싶을 때

</div>
</details>

1번 방법은 널리 알려져 있으며, 2번 방법에 대해 포스팅한다.

## Colima 개념

Lima(Linux virtual machine on macOS) 위에 Docker, 컨테이너 런타임 구동하여, macOS에서 네이티브 리눅스 컨테이너 환경 제공하는 경량 가상화 플랫폼

### 구성요소

|     **구성요소**     | 설명                             | 역할                             |
| :------------------: | -------------------------------- | -------------------------------- |
| Lima(Linux on macOS) | macOS 위에서 경량 리눅스 VM 실행 | Colima 기반 OS 제공              |
|  Container Runtime   | Docker 또는 containered          | 실제 컨테이너 실행시키는 엔진    |
|      Colima CLI      | Colima 명령줄 인터페이스         | VM 생성, 시작, 중지 등 관리 수행 |

### 설치

#### Prerequisite

Homebrew가 설치되어 있어야 한다. 설치되어 있지 않은 경우, 아래 명령어를 통해 설치한다.

```text
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

#### 필수 패키지 설치

```text
brew install colima docker docker-compose
```

macOS는 리눅스 커널이 없기 때문에, colima가 리눅스 VM 생성 및 Docker Engine을 설치하는 역할을 하고, 여기서 docker, docker-compose는 colima와 연동되어 다음과 같은 역할을 한다.

- docker: Docker CLI 명령어
- docker-compose: 다수 컨테이너 오케스트레이션 명령어(Compose v2)

### 실행

아래 명령어를 실행하여 Colima VM을 생성한다.

```text
# VM의 프로파일명 docker, 4 CPU, 8 GB 메모리, 50 GB 스토리지로 생성
colima start --profile docker --cpu 4 --memory 8 --disk 50
```

위 명령어를 실행해서 생성된 파일 경로는 다음과 같다.

```text
/Users/<사용자이름>/.colima/_lima/<프로파일명>/
```

#### 주요 파일

|      **파일명**       | 설명                                                                                                            |
| :-------------------: | --------------------------------------------------------------------------------------------------------------- |
|       diffdisk        | Colima VM의 리눅스 디스크 이미지 (qcow2 포맷) 즉, 컨테이너 데이터, Docker 엔진, 이미지, 모든 파일이 여기에 저장 |
|      config.yaml      | VM 설정 파일 (CPU, 메모리, 네트워크 정보 등)                                                                    |
|      serial.log       | VM 부팅 및 로그 출력                                                                                            |
| ssh.sock, docker.sock | macOS의 Docker CLI가 이 소켓을 통해 VM 안의 Docker 엔진과 통신                                                  |

- 현재 프로파일 설정 확인

```text
colima list --verbose
```

#### Docker 엔진 연결 확인

```text
docker context ls
# 별표(\*)가 생성된 프로파일에 있으면 연결 OK

# 아니라면 전환
docker context use colima || docker context use colima-docker

# 아래 명령어를 통해 확인
docker version   # Server 섹션이 보여야 정상
docker run hello-world
```

### 주요 명령어

#### Colima VM 중단

```text
colima stop -p <프로파일명>
```

#### Colima VM 삭제

- ⚠️ 이 명령은 해당 VM 내 모든 Docker 이미지, 컨테이너, 볼륨을 함께 삭제한다.

```text
colima delete --profile <프로파일명>
```

- 삭제 후 경로 확인:

```text
ls ~/.colima/_lima/
# <프로파일명> 폴더가 없어졌다면 완전히 삭제 완료
```
