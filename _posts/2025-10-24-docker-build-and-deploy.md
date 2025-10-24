---
layout: post
title: "[Docker] Build and Deploy"
date: 2025-10-24 21:38 +0900
categories: [Docker]
tags: [Docker]
image:
  path: /assets/img/2025-10-24-docker-build-and-deploy/favicon.jpeg
---

macOS에서 빌드한 Docker 이미지를 Windows와 Ubuntu에 배포하기 위한 방법을 포스팅한다.

## Dockerfile

Dockerfile은 도커 이미지를 만드는 데 필요한 일련의 명령어들을 담고 있는 텍스트 파일이다. 확장자 없이 `Dockerfile` 이라는 파일로 저장하며 아래는 예시이다.

4단계의 스테이지로 구성된 `Multi-stage` 빌드라고 한다.

```dockerfile
# 1단계: 개발 의존성을 설치하기 위한 스테이지
FROM node:20-alpine AS development-dependencies-env
# 현재 디렉토리의 모든 파일을 컨테이너의 /app 디렉토리로 복사
COPY . /app
# 작업 디렉토리를 /app으로 설정
WORKDIR /app
# package-lock.json을 기반으로 모든 의존성(개발 의존성 포함) 설치
RUN npm ci

# 2단계: 프로덕션 의존성만 설치하기 위한 스테이지
FROM node:20-alpine AS production-dependencies-env
# package.json과 package-lock.json만 복사 (소스 코드는 제외)
COPY ./package.json package-lock.json /app/
# 작업 디렉토리를 /app으로 설정
WORKDIR /app
# 개발 의존성을 제외하고 프로덕션 의존성만 설치
RUN npm ci --omit=dev

# 3단계: 애플리케이션 빌드를 위한 스테이지
FROM node:20-alpine AS build-env
# 모든 소스 코드를 컨테이너로 복사
COPY . /app/
# 개발 의존성 스테이지에서 설치된 node_modules를 복사
COPY --from=development-dependencies-env /app/node_modules /app/node_modules
# 작업 디렉토리를 /app으로 설정
WORKDIR /app
# 애플리케이션 빌드 실행
RUN npm run build

# 4단계: 최종 프로덕션 이미지 스테이지
FROM node:20-alpine
# package.json과 package-lock.json을 복사
COPY ./package.json package-lock.json /app/
# 프로덕션 의존성만 포함된 node_modules를 복사
COPY --from=production-dependencies-env /app/node_modules /app/node_modules
# 빌드된 애플리케이션 파일들을 복사
COPY --from=build-env /app/build /app/build
# 작업 디렉토리를 /app으로 설정
WORKDIR /app
# 컨테이너 시작 시 실행할 명령어 (애플리케이션 시작) (기본값)
# CMD ["npm", "run", "start"]

# 포트 노출 (React Router 기본 포트는 3000)
EXPOSE 3000
# 컨테이너 시작 시 실행할 명령어 (애플리케이션 시작)
# HOST=0.0.0.0으로 설정하여 모든 네트워크 인터페이스에서 접근 가능하도록 함
CMD ["sh", "-c", "HOST=0.0.0.0 npm run start"]
```

`FROM`은 Dockerfile의 시작점으로서, 생성할 이미지의 기반을 지정하는 명령어이다. 해당 이미지는 로컬에 없으면 `Docker Hub`와 같은 레지스트리로부터 pull한다.

## Build

### 기본 build

기본적으로 이미지를 빌드하는 명령어는 다음과 같다. 단, **같은 아키텍처(예: 둘 다 amd64)**끼리는 잘 돌아가지만, 다른 아키텍처 (예: arm64 Mac ↔ x64 Ubuntu)에서 그대로 사용할 수 없다.

```bash
docker build -t my-org/my-app:latest .
# docker build: Dockerfile을 바탕으로 이미지를 생성
# -t my-org/my-app:latest: 이미지에 이름(my-org/my-app)과 태그(latest)를 붙임
# 태그는 버전 번호와 같이 :v1, :prod, :2025-10-24 이런 식으로도 씀.
# .: 현재 디렉터리를 컨텍스트로 사용 (Dockerfile과 소스코드를 여기서 읽음)
```

## buildx

### 개념

Docker 컨테이너는 OS보다 CPU 아키텍처에 민감하다.

- Ubuntu 서버 대부분: amd64 (x86_64)
- Mac (M1/M2/M3): arm64
- Windows: WSL2에서 리눅스 컨테이너 돌릴 때 → amd64 또는 arm64용 리눅스 이미지 사용

즉, 컨테이너가 특정 CPU 아키텍처에 동일하게 실행되도록 해주는 것이 buildx이다.

### 설치

아래 명령어를 통해 buildx를 설치한다.

```bash
brew install docker-buildx
```

Docker CLI가 buildx를 CLI 플러그인으로 인식할 수 있도록 연결하는 작업이 추가적으로 필요하다. Docker는 ~/.docker/cli-plugins/ 아래에 있는 실행 파일들을 서브커맨드처럼 취급한다. 방금 설치한 docker-buildx 바이너리를 그 폴더에 심볼릭 링크로 연결해야 한다.

```bash
mkdir -p ~/.docker/cli-plugins
ln -sfn /opt/homebrew/opt/docker-buildx/bin/docker-buildx ~/.docker/cli-plugins/docker-buildx
# 만약 Homebrew가 다른 경로에 설치돼 있으면 /opt/homebrew/... 대신 brew --prefix docker-buildx 로 실제 경로 확인 후 맞춰줘야 한다. Apple Silicon 기본 경로는 /opt/homebrew/... 이다.
```

아래 명령어를 통해 buildx가 정상적으로 인식되는지 확인한다.

```bash
docker buildx version
```

### buildx builder 생성

아래 명령어를 통해 buildx의 builder를 생성한다. buildx create로 builder를 만드는 이유는 **“Docker의 기본 빌드 엔진이 멀티 아키텍처 빌드를 지원하지 않기 때문”**이며, 여러 개의 builder를 만들어서 서로 다른 빌드 환경(플랫폼, 빌드 캐시, 저장소)을 독립적으로 관리하기 위해 사용한다.

Docker는 이런 문제를 해결하기 위해 `BuildKit`이라는 고급 빌드 엔진을 제공하며, buildx는 BuildKit을 제어하는 인터페이스이다. 이 BuildKit 엔진은 기본적으로 자동으로 활성화되지 않기 때문에 “빌드용 인스턴스(builder)” 를 만들어줘야 한다.

아래 명령어는 "BuildKit 기반 빌더 컨테이너를 새로 만들고, 그 빌더에게 여러 플랫폼(arm64, amd64 등)을 동시에 빌드할 수 있는 능력을 부여한 다음, 앞으로 docker buildx build 할 때 그 빌더를 기본으로 쓰겠다."는 의미이다.

특정 버전 이후 colima는 기본적으로 buildx가 제공된다고 하므로, builder 존재 여부를 확인 후 생성한다. 주요 명령어는 다음과 같다.

```bash
# builder 목록 확인
docker buildx ls

# builder가 없는 경우 생성
docker buildx create --name multi --use

# builder 정상 동작 확인
docker buildx inspect --bootstrap

# builder 사용 선택 (필요시)
docker buildx use multi

# builder 삭제 (필요시)
docker buildx rm multi
```

### Build

#### 빌드된 이미지를 로컬에 저장

```bash
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t my-app:latest \
  .

# --platform linux/amd64,linux/arm64 → 인텔/AMD 서버용(x86_64) + Mac ARM용 둘 다 한 번에 만든다
# -t my-app:latest → 결과 이미지를 이 이름으로 태그한다
# . → 현재 디렉터리의 Dockerfile을 사용
```

이미지가 저장되는 경로는 다음과 같다.

|     **OS**     | 경로                                                             |
| :------------: | ---------------------------------------------------------------- |
| macOS / Colima | ~/.colima/\_lima/<프로필>/diffdisk (VM 내부 /var/lib/docker/ 안) |
| Linux (Ubuntu) | /var/lib/docker/                                                 |
| Windows (WSL2) | \\wsl$\docker-desktop-data\version-pack-data\community\docker\   |

다음 명령어를 통해 빌드된 이미지를 확인한다.

```bash
docker images
```

다음 명령어를 통해 실행한다.

```bash
docker run -p 3000:3000 my-app:latest
# -d(detached mode): 백그라운드에서 실행하고, 터미널 즉시 반환
```

이미지를 로컬 파일로 저장하려면 다음 명령어를 실행한다.

```bash
docker save -o my-app.tar my-app:latest
```

저장된 이미지는 다른 PC에서 다음 명령어로 불러올 수 있다.

```bash
docker load -i my-app.tar
```

#### 빌드된 이미지를 바로 Registry에 업로드

```bash
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t ghcr.io/내계정/내이미지:latest \
  --push \
  .

# --platform linux/amd64,linux/arm64 → 인텔/AMD 서버용(x86_64) + Mac ARM용 둘 다 한 번에 만든다
# -t ghcr.io/내계정/my-app:latest → 결과 이미지를 이 이름으로 태그한다 (예: GitHub Container Registry)
# --push → 빌드된 멀티 아키텍처 이미지를 레지스트리에 업로드한다
# . → 현재 디렉터리의 Dockerfile을 사용
```
