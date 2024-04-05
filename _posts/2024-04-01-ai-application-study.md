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

[virtualenv][post-virtualenv]를 설정한다.

## Jupyter Notebook

`.ipynb` 확장자를 가진 파일을 생성한다.

## dotenv

`.env` 파일을 생성하고 `OPENAI_API_KEY`에 자신의 API KEY를 입력해야 한다.

`OpenAI` module은 해당 환경변수를 찾아서 사용한다.

```text
OPENAI_API_KEY="sk-xxxx"
```

## Call Basic LLM

[link][openai-models]에서 `OpenAI`에서 지원하는 model 목록 확인할 수 있다.

- Large Language Model (LLM): 언어를 이해하고 생성하는 데 사용되는 대규모 인공지능 모델. default로 `text-davinci-003`를 사용한다.
  - [Deprecations][openai-deprecations]에 의하면 `text-davinci-003` model은 2024-01-04부로 지원이 중단되었다.
  - [Pricing][openai-pricing]를 참고하여 token 당 저렴한 모델을 설정하여 사용하면 된다.

```python
from langchain.llms.openai import OpenAI

llm = OpenAI(model="gpt-3.5-turbo-instruct")
llm.predict("How many planets are there?")
```

- Chat Model: 자연어 대화에 최적화된 모델. default로 `gpt-3.5-turbo`를 사용한다.

```python
from langchain.chat_models import ChatOpenAI

chat = ChatOpenAI(
  temperature=0.1
)
chat.predict("How many planets are there?")
```

## Predict using Message Schema

```python
from langchain.chat_models import ChatOpenAI
from langchain.schema import HumanMessage, AIMessage, SystemMessage

chat = ChatOpenAI(temperature=0.1)

messages = [
    SystemMessage(content="You are a geography expert. And you are only reply in Italian."),
    AIMessage(content="Ciao mi chiamo Paolo!"),
    HumanMessage(content="What is the distance between Maxico and Thailand. Also What is your name?")
]

chat.predict_messages(messages)
```

temperature
: 0 - 1 사이 소수점을 입력하여 창의성을 설정한다. 높을수록 창의성과 무작위성 이 높다.

HumanMessage
: 사람이 질문하는 자연어 message

AIMessage
: AI가 대답하는 message

SystemMessage
: LLM에 설정을 전달하는 message. 미리 학습

## Prompt Template

PromptTemplate
: string 기반 template을 prompt하기 위해 사용

```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts import PromptTemplate

template = PromptTemplate.from_template(
    "What is the distance between {country_a} and {country_b}"
)

prompt = template.format(country_a="Maxico", country_b="Thailand")

chat = ChatOpenAI(temperature=0.1)

chat.predict(prompt)
```

ChatPromptTemplate
: message로부터 template을 생성하여 prompte하기 위해 사용

```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts import ChatPromptTemplate

template = ChatPromptTemplate.from_messages([
    ("system", "You are a geography exprt. And you only reply in {language}."),
    ("ai", "Ciao mu chiamo {name}!"),
    ("human", "What is the distance between {country_a} and {country_b}. Also, what is your name?")
])

prompt = template.format_messages(
    language="Greek",
    name="Socrates",
    country_a="Maxico",
    country_b="Thailand"
)

chat.predict_messages(prompt)
```

[nomadcoders-fullstack-gpt]: https://nomadcoders.co/fullstack-gpt
[platform-openai]: https://platform.openai.com
[langchain]: https://www.langchain.com
[streamlit]: https://streamlit.io
[pinecone]: https://www.pinecone.io
[huggingface]: https://huggingface.co
[fastapi]: https://fastapi.tiangolo.com
[post-virtualenv]: https://applic8ion.github.io/posts/python-virtualenv/
[openai-models]: https://platform.openai.com/docs/models
[openai-deprecations]: https://platform.openai.com/docs/deprecations
[openai-pricing]: https://openai.com/pricing
