---
layout: post
title: "[AI] AI Application Study"
date: 2024-04-01 23:42 -0700
categories: [AI, Study]
tags: [AI, GPT, LangChain]
mermaid: true
image:
  path: /assets/img/2024-04-01-ai-application-study/ai_parrot.JPG
---

이미지 출처: [Nomad Coders][nomadcoders-fullstack-gpt]

## Requirements

- [OpenAI][platform-openai]: ChatGPT 관리
- [LangChain][langchain]: Large Language Model (LLM) 기반 application 개발을 위한 framework
- [Streamlit][streamlit]: Python 코드로 빠르게 web 기반 UI 제작해주는 모듈
- [Pinecone][pinecone]: Vector를 위한 Database
- [Huggingface][huggingface]: 다양한 model 적용
- [FastAPI][fastapi]: API 구축 framework

### Usage Limits for OpenAI

[OpenAI][platform-openai]에서 `Settings` > `Limits` > `Usage limits`에서 설정 가능하다.

## virtualenv

[link][post-virtualenv]를 통해 `virtualenv`를 설정한다.

## Jupyter Notebook

`.ipynb` 확장자를 가진 파일을 생성한다.

## Call LLM

- Large Language Model (LLM): 언어를 이해하고 생성하는 데 사용되는 대규모 인공지능 모델
- Chat Model: 대화형으로 설계된 모델

[nomadcoders-fullstack-gpt]: https://nomadcoders.co/fullstack-gpt
[platform-openai]: https://platform.openai.com
[langchain]: https://www.langchain.com
[streamlit]: https://streamlit.io
[pinecone]: https://www.pinecone.io
[huggingface]: https://huggingface.co
[fastapi]: https://fastapi.tiangolo.com
[post-virtualenv]: https://applic8ion.github.io/posts/python-virtualenv/
