---
layout: post
title: "[n8n] Setup n8n in a Local Environment"
date: 2026-02-17 11:17 +0900
categories: [Automation]
tags: [n8n, Automation, LLM, MCP, CluadeCode]
image:
  path: /assets/img/2026-02-17-n8n-setup/thumbnail.png
---

본 포스팅은 로컬 환경에서 n8n 환경 설정하는 법을 소개한다.

## Setup Cloudflare

`Cloudflare Tunnel`을 사용하여 로컬에서 실행 중인 서비스를 외부 인터넷에서 접속할 수 있도록 연결해 주어야 한다.

아래는 Cloudflare Tunnel 설치 및 실행 방법이다.

```bash
# install Cloudflare
brew install cloudflared

# Create Tunnel URL
cloudflared tunnel --url http://localhost:5678

2026-02-17T04:53:22Z INF +--------------------------------------------------------------------------------------------+
2026-02-17T04:53:22Z INF |  Your quick Tunnel has been created! Visit it at (it may take some time to be reachable):  |
2026-02-17T04:53:22Z INF |  https://xxxx-xxxx-xxxx.trycloudflare.com                                       |
2026-02-17T04:53:22Z INF +--------------------------------------------------------------------------------------------+
```

`.env` 파일을 생성하고 위에서 생성된 Cloudflare URL을 `WEBHOOK_URL` 환경변수로 설정한다.

```bash
# PostgreSQL 데이터베이스 설정
POSTGRES_DB=n8n
POSTGRES_USER=n8n_user
POSTGRES_PASSWORD=your_postgre_password_here

# n8n 인증 설정
N8N_USER=admin
N8N_PASSWORD=your_admin_password_here
N8N_SECURE_COOKIE=false
N8N_API_KEY=your_api_key_here
WEBHOOK_URL=https://xxxx-xxxx-xxxx.trycloudflare.com
```

위 방법은 Cloudflare Tunnel을 실행할 때마다 URL이 변경되는 문제가 있으므로, Cloudflare에 도메인을 등록하고 Cloudflare Tunnel을 사용하는 것이 좋다.

Cloudflare에 도메인을 등록하고 사용하는 방법은 다음 포스팅에서 설명한다.

## Docker Compose

`n8n` 실행을 위한 이미지와 데이터를 저장할 `PostgreSQL` 을 동시 실행하기 위해 `Docker Compose`를 구성해야 한다.

아래는 `docker-compose.yml` 파일 예시이다.

아래 중괄호 `{}` 로 표기된 부분은 `.env ` 파일에 설정해야 한다.

```yaml
services:
  n8n:
    image: n8nio/n8n:latest
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgresql
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=${POSTGRES_DB}
      - DB_POSTGRESDB_USER=${POSTGRES_USER}
      - DB_POSTGRESDB_PASSWORD=${POSTGRES_PASSWORD}
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=${N8N_USER}
      - N8N_BASIC_AUTH_PASSWORD=${N8N_PASSWORD}
      - N8N_EDITOR_BASE_URL=http://localhost:5678/
      - WEBHOOK_URL=${WEBHOOK_URL}
      - GENERIC_TIMEZONE=Asia/Seoul # 시간대 설정 (선택 사항)
      - N8N_SECURE_COOKIE=${N8N_SECURE_COOKIE}
    volumes:
      - ./n8n_data:/home/node/.n8n
    depends_on:
      - postgresql

  postgresql:
    image: postgres:17
    restart: unless-stopped
    environment:
      - POSTGRES_DB=${POSTGRES_DB}
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
    volumes:
      - ./pg_data:/var/lib/postgresql/data
```

## Build

아래 명령어를 통해 위에서 작성한 `Docker Compose`를 실행한다.

```bash
docker-compose up -d
# -d: 백그라운드에서

# 실행 상태 확인
docker compose ps

# 로그 확인
docker compose logs n8n 실행
```

## Check Connection

브라우저를 통해 [http://localhost:5678](http://localhost:5678)에 접속한다.

## Connect to LLM

다음 과정은 LLM을 통해 n8n을 제어할 수 있도록 설정하는 과정이다.

### Install n8n-MCP

LLM이 `MCP`를 통해 `n8n`과 통신할 수 있도록 `n8n-MCP`를 설치한다.

[n8n-mcp](https://github.com/czlonkowski/n8n-mcp)를 참고하여 `n8n-MCP`를 설치한다.

```bash
# Run directly with npx (no installation needed!)
npx n8n-mcp
```

`.mcp.json` 파일을 생성하고 아래와 같이 설정한다.

`N8N_API_KEY`는 [n8n](http://localhost:5678/settings/api)에서 발급받은 API 키를 `.env` 파일에 설정한 후 사용한다.

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "--init",
        "-e",
        "MCP_MODE=stdio",
        "-e",
        "LOG_LEVEL=error",
        "-e",
        "DISABLE_CONSOLE_OUTPUT=true",
        "-e",
        "N8N_API_URL=http://localhost:5678",
        "-e",
        "N8N_API_KEY=${N8N_API_KEY}",
        "-e",
        "WEBHOOK_SECURITY_MODE=moderate",
        "ghcr.io/czlonkowski/n8n-mcp:latest"
      ]
    }
  }
}
```

아래 명령어를 통해 설치 및 실행 여부를 확인한다.

```bash
# Command in Claude Code
/mcp

─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
 Manage MCP servers
 1 server

   Project MCPs (/Users/taewon/Documents/projects/n8n/scripts/.mcp.json (file does not exist))
 ❯ n8n-mcp · ✔ connected
```

### Install n8n-Skills

[n8n-skills](https://github.com/czlonkowski/n8n-skills)를 참고하여 `n8n-skills`를 설치한다.

프로젝트 루트에 `.claude/skills` 디렉토리에 7개의 skill이 존재해야 한다.

#### Method 1: Plugin Installation (Recommended)

```bash
# Install directly as a Claude Code plugin
/plugin install czlonkowski/n8n-skills
```

#### Method 2: Manual Installation

```bash
# 1. Clone this repository
git clone https://github.com/czlonkowski/n8n-skills.git

# 2. Copy skills to your Claude Code skills directory
cp -r n8n-skills/skills/* ~/.claude/skills/

# 3. Reload Claude Code
# Skills will activate automatically
```

아래 명령어를 통해 설치 여부를 확인한다.

```bash
# Command in Claude Code
/skills

─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
 Skills
 7 skills

 Project skills (.claude/skills)
 n8n-workflow-patterns · ~82 description tokens
 n8n-validation-expert · ~80 description tokens
 n8n-code-javascript · ~68 description tokens
 n8n-mcp-tools-expert · ~68 description tokens
 n8n-node-configuration · ~65 description tokens
 n8n-expression-syntax · ~60 description tokens
 n8n-code-python · ~52 description tokens
```

### Create SOP.md

`SOP(Standard Operating Procedure)`는 n8n 워크플로우를 실행할 때 필요한 절차를 정리한 문서이다.

[SOP.md 작성 - 워크플로우 설계도 만들기](https://github.com/citizendev9c/yt-assets/blob/main/automation/n8n/claude-code-workflow-builder-26-02-07/README.md#sopmd-작성---워크플로우-설계도-만들기)를 통해 SOP.md 파일을 생성한다.

### Create CLAUDE.md

`CLAUDE.md`는 LLM(`Claude Code`)이 n8n 워크플로우를 생성할 때 따라야 할 가이드 문서이다.

[CLAUDE.md 설정 - 워크플로우 제작 가이드](https://github.com/citizendev9c/yt-assets/blob/main/automation/n8n/claude-code-workflow-builder-26-02-07/README.md#claudemd-설정---워크플로우-제작-가이드)를 통해 CLAUDE.md 파일을 생성한다.

## Commands

```bash
# 서비스 시작
docker compose up -d

# 서비스 중지
docker compose down

# 서비스 재시작
docker compose restart

# 특정 서비스만 재시작
docker compose restart n8n

# 서비스 중지 (볼륨 및 네트워크 제거)
docker compose down -v
```

## Reference

- [셀프호스팅(Docker) CLI](https://wikidocs.net/290898)
- [자동화 끝판왕 n8n, 이렇게 설치하면 평생 무료입니다! (웹훅 설정, 버전 업데이트 포함)](https://www.youtube.com/watch?v=DhuaKAW819s&list=PLmOOOSr4WNk1GTWZjRwgLsAK5xp6K1fs6)
- [n8n-mcp](https://github.com/czlonkowski/n8n-mcp)
- [n8n-skills](https://github.com/czlonkowski/n8n-skills)
- [SOP.md 작성 - 워크플로우 설계도 만들기](https://github.com/citizendev9c/yt-assets/blob/main/automation/n8n/claude-code-workflow-builder-26-02-07/README.md#sopmd-작성---워크플로우-설계도-만들기)
- [CLAUDE.md 설정 - 워크플로우 제작 가이드](https://github.com/citizendev9c/yt-assets/blob/main/automation/n8n/claude-code-workflow-builder-26-02-07/README.md#claudemd-설정---워크플로우-제작-가이드)
- [https://github.com/applic8ion/n8n](https://github.com/applic8ion/n8n)
