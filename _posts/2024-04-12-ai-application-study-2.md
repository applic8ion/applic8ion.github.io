---
layout: post
title: "[AI] AI Application Study [2/n]"
date: 2024-04-12 00:27 -0700
categories: [AI, Study]
tags: [AI, GPT, LangChain]
mermaid: true
pin: true
image:
  path: /assets/img/2024-04-01-ai-application-study/ai_parrot.JPG
---

## LengthBasedExampleSelector

설정에 따라 알맞은 example을 선택해준다.

example이 많을수록 비용이 증가한다.

`LengthBasedExampleSelector` function에서 `max_length`에 따라 `examples` 중에서 임의의 example이 선택된다.

[[AI] AI Application Study [2/n]][2024-04-01-ai-application-study-1] 에서 `FewShotPromptTemplate` 예제를 다음과 같이 수정한다.

아래 code는 Brazil이 example에 존재하지 않지만, max_length를 늘려줌으로써 임의의 example이 출력된다.

```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts import PromptTemplate
from langchain.prompts.few_shot import FewShotPromptTemplate
from langchain.callbacks import StreamingStdOutCallbackHandler
from langchain.prompts.example_selector import LengthBasedExampleSelector

chat = ChatOpenAI(
    temperature=0.1,
    streaming=True, # Check the chatmodel response creation process
    callbacks=[StreamingStdOutCallbackHandler()]
)

examples = [
    {
        "question": "What do you know about France?",
        "answer": """
        Here is what I know:
        Capital: Paris
        Language: French
        Food: Wine and Cheese
        Currency: Euro
        """,
    },
    {
        "question": "What do you know about Italy?",
        "answer": """
        I know this:
        Capital: Rome
        Language: Italian
        Food: Pizza and Pasta
        Currency: Euro
        """,
    },
    {
        "question": "What do you know about Greece?",
        "answer": """
        I know this:
        Capital: Athens
        Language: Greek
        Food: Souvlaki and Feta Cheese
        Currency: Euro
        """,
    },
]

example_template = """
    Human: {question}
    AI: {answer}
"""

example_prompt = PromptTemplate.from_template(example_template)

example_selector = LengthBasedExampleSelector(
    examples=examples,
    example_prompt=example_prompt, # how long of formated data
    max_length=180 # the number of examples
)

prompt = FewShotPromptTemplate(
    example_prompt=example_prompt,
    example_selector=example_selector,
    suffix="Human: What do you know about {country}?",
    input_variables=["country"]
)

prompt.format(country="Brazil")
```

## BaseExampleSelector

`BaseExampleSelector`를 통해 특정 logic을 이용하여 example을 선택할 수 있다.

아래 코드는 `BaseExampleSelector`를 상속한 `RandomExampleSelector` class를 통해 임의의 example을 선택한다.

```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts import PromptTemplate
from langchain.prompts.few_shot import FewShotPromptTemplate
from langchain.callbacks import StreamingStdOutCallbackHandler
from langchain.prompts.example_selector.base import BaseExampleSelector

chat = ChatOpenAI(
    temperature=0.1,
    streaming=True, # Check the chatmodel response creation process
    callbacks=[StreamingStdOutCallbackHandler()]
)

examples = [
    {
        "question": "What do you know about France?",
        "answer": """
        Here is what I know:
        Capital: Paris
        Language: French
        Food: Wine and Cheese
        Currency: Euro
        """,
    },
    {
        "question": "What do you know about Italy?",
        "answer": """
        I know this:
        Capital: Rome
        Language: Italian
        Food: Pizza and Pasta
        Currency: Euro
        """,
    },
    {
        "question": "What do you know about Greece?",
        "answer": """
        I know this:
        Capital: Athens
        Language: Greek
        Food: Souvlaki and Feta Cheese
        Currency: Euro
        """,
    },
]

class RandomExampleSelector(BaseExampleSelector):
    def __init__(self, examples):
        self.examples = examples

    def add_example(self, example):
        self.examples.append(self)

    def select_examples(self, input_variables):
        from random import choice
        return [choice(self.examples)]

example_template = """
    Human: {question}
    AI: {answer}
"""

example_prompt = PromptTemplate.from_template(example_template)

example_selector = RandomExampleSelector(
    examples=examples,
)

prompt = FewShotPromptTemplate(
    example_prompt=example_prompt,
    example_selector=example_selector,
    suffix="Human: What do you know about {country}?",
    input_variables=["country"]
)

prompt.format(country="Brazil")
```

[2024-04-01-ai-application-study-1]: https://applic8ion.github.io/posts/ai-application-study-1/
