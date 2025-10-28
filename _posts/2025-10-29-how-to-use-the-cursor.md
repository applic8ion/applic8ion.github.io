---
layout: post
title: "[Cursor] How to use the Cursor"
date: 2025-10-29 00:52 +0900
categories: [IDE, Cursor]
tags: [IDE, Cursor]
image:
  path: /assets/img/2025-10-29-how-to-use-the-cursor/thumbnail.png
---

IDE인 Cursor 사용법을 포스팅한다.

## Cursor Rules

AI와 채팅 시 답변받을 지시사항을 사전에 정의할 수 있는 기능이다. 기존 방식은 프로젝트 폴더 내 `.cursorrules` 파일을 생성하는 것이다. 내용은 [링크](https://gist.github.com/serranoarevalo/e71acb05ad5d7debc98037fc4e8396bd)를 참고한다

현재(2025-10-29) 변경된 방식은 [링크](https://docs.cursor.com/context/rules)를 참고한다. `.cursor/rules` 폴더 내 `*.mdc` 파일을 생성하여 작성해야 한다. `Cursor Settings > Rules & Memories` 탭에서 새 규칙을 추가하거나 모든 규칙을 관리할 수 있다.

## Chatting

코드 내에서 특정 영역에 대해 질문하거나 리팩토링하고 싶다면, 해당 영역을 블록 씌운 후 `Command + K`을 누른다.

언제든 LLM 모델과 채팅하고 싶다면 `Command + L`을 누른다. 여기서도 특정 영역을 블록 씌어서 Context를 지정하거나, 채팅창에 `@`를 타이핑하면, LLM이 아래 내용을 참고하여 답변하게 할 수 잇다.

- `@Files & Folders`: 파일/폴더를 지정한다.
- `@Web`: 웹검색을 한다.
- `@Docs`: 특정 문서(매뉴얼 등)를 참고한다. 기본적으로 제공하는 것도 있지만, 미제공되는 경우, `Cursor Settings > Indexing & Docs` 탭에서 새 문서를 추가하거나 모든 문서를 관리할 수 있다. 여기서 참고할 문서의 URL을 추가한다. 예시는 다음과 같다.
  - https://orm.drizzle.team/docs/
  - https://supabase.com/docs/

Windows에서는 각각 `Ctrl + K`와 `Ctrl + L` 이다.
