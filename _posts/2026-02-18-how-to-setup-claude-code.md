---
layout: post
title: "[Claude Code] Claude Code Setup Guide"
date: 2026-02-18 00:07 +0900
categories: [AI, Claude Code]
tags: [AI, Claude Code]
image:
  path: /assets/img/2026-02-18-how-to-setup-claude-code/thumbnail.png
---

본 포스팅은 Claude Code를 설정하는 방법을 소개한다.

## Install

[링크](https://code.claude.com/docs/en/quickstart#step-1-install-claude-code)를 통해 플랫폼 별 설치 명령어를 확인한다.

### macOS, Linux, WSL

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

### Windows PowerShell

```bash
irm https://claude.ai/install.ps1 | iex
```

### Windows CMD

```bash
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

macOS에서 실행 시, 화면이 멈춘 것처럼 보일 수 있는데, 기다리면 아래와 같이 표시된다.

```bash
Setting up Claude Code...

✔ Claude Code successfully installed!

  Version: 2.1.44

  Location: ~/.local/bin/claude


  Next: Run claude --help to get started

✅ Installation complete!
```

## Login

터미널에서 `claude`를 입력하여 Claude Code를 실행하면 테마를 선택하게 되고, 로그인 방식을 선택하라는 메시지가 표시된다.

```bash
# Run Claude Code
claude

# Select Login Method
Claude Code can be used with your Claude subscription or billed based on API usage through your Console account.

 Select login method:

# 1. 유료 버전 구독중일 시 선택
 ❯ 1. Claude account with subscription · Pro, Max, Team, or Enterprise
# 2. API Key를 연동하여 사용량에 따라 과금
   2. Anthropic Console account · API usage billing
# 3. 3rd-party 플랫폼 연동
   3. 3rd-party platform · Amazon Bedrock, Microsoft Foundry, or Vertex AI
```

1번을 선택한 후 브라우저에서 로그인하면 코드가 생성되고, 해당 코드를 붙여넣기 하여 인증하면 된다.

인증 토큰은 [링크](https://claude.ai/settings/claude-code)에서 확인할 수 있다.

## auto-accept edits

`shift + tab`을 눌러서 권한을 설정할 수 있다.

- **none**: 기본 권한 요청을 줌
- **accept edits on**: 파일 읽기, 쓰기, 실행 권한을 줌 (중요한 작업은 권한 요청함)
- **plan mode on**: 실행 전 계획을 보여줌

권한 요청 없이 모든 권한을 주려면 아래 명령어로 Claude Code를 실행하면 `bypass permissions on`을 활성화할 수 있게 된다.

```bash
claude --dangerously-skip-permissions
```

## Context

아래 명령어를 통해 사용량을 확인할 수 있다.

`SOP.md`, `CLAUDE.md` 파일이 존재할 경우, 이를 재참고하면 되므로 주기적으로 `/clear` 명령을 통해 컨텍스트를 초기화하는 것이 좋다.

```bash
# Show usage
/context

# Clear context
/clear
```
