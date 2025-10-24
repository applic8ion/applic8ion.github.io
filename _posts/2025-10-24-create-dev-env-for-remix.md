---
layout: post
title: "[Remix] Remix 프로젝트 생성"
date: 2025-10-24 12:09 +0900
categories: [WebDev, Remix]
tags: [Remix]
image:
  path: /assets/img/2025-10-24-create-dev-env-for-remix/favicon.jpeg
---

docker를 활용하여 Node.js 설치없이 Remix 프로젝트를 생성하는 방법을 포스팅한다.

## Prerequisite

### Node.js 컨테이너 기반 Remix 프로젝트 생성

#### Node.js 컨테이너 실행

Docker 기반 환경 구축을 위해 [Colima VM을 실행](https://applic8ion.github.io/posts/docker-for-macos/#Execution)하고, 다음 명령어로 [Docker Hub](https://hub.docker.com)를 통해 Node.js 이미지를 pull한다.

```bash
# Node.js Docker 이미지 pull. 현 시점 기준 최신 버전
docker pull node:22-alpine

docker run -it --rm \
  -v $PWD:/app \
  -w /app \
  -p 5173:5173 \
  --entrypoint sh node:22-alpine

# -it → 터미널과 연결(interactive + tty)
# --rm → 종료되면 자동 삭제
# --entrypoint sh → Node.js 실행 대신 셸(sh) 실행
# -v $PWD:/app → 현재 macOS 디렉토리를 컨테이너의 /app 폴더에 연결 (Mount)
# -w /app → 컨테이너의 기본 작업 디렉토리를 /app으로 설정
# -p 컨테이너의 특정 포트를 호스트의 특정 포트로 포트포워딩
```

#### git 설치

```bash
apk update
apk add git

git config --global user.name <github 계정>
git config --global user.email <email 주소>
```

### Project Creation

다음 명령어를 통해 프로젝트를 설치한다. 모든 옵션은 yes로 선택한다.

```bash
npx create-react-router@7.0.1 wemake
```

### Execution

아래 명령어를 실행하고, <http://localhost:5173>에 접속한다.

```bash
npm run dev -- --host
```

- `--` (double dash) 역할:
  - \-\-는 npm의 특별한 구분자
  - \-\- 앞의 옵션들은 npm에게 전달
  - \-\- 뒤의 옵션들은 실행될 스크립트에게 전달
- `--host` 옵션 기능:
  - 기본값: localhost 또는 127.0.0.1에만 바인딩
  - \-\-host 사용: 모든 네트워크 인터페이스 (0.0.0.0)에 바인딩

#### 다른 예시

```bash
npm run dev -- --port 3000
npm run dev -- --host 0.0.0.0 --port 3000
npm run build -- --mode production
```
