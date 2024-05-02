---
layout: post
title: "[AI] AI Application Study [3/n]"
date: 2024-04-20 22:16 -0700
categories: [AI, Study]
tags: [AI, GPT, LangChain]
mermaid: true
pin: true
image:
  path: /assets/img/2024-04-01-ai-application-study/ai_parrot.JPG
---

# Memory

이전 대화 내용을 기반으로 답변할 수 있도록 `Langchain`에서 제공하는 다양한 memory에 대해 설명한다.

각 memory는 동일한 API를 제공한다.

예를 들어, `Langchain`에서 제공하는 모든 memory는 `save_context`와 `load_memory_variables`를 제공한다.

## ConversationBufferMemory

[ConversationBufferMemory][conversation-buffer]는 단순히 이전 conversation 전체를 저장한다.

conversation이 길수록 메모리가 커지므로, 비효율적이다.

text를 자동 완성할 때 유용하다.

```python
from langchain.memory import ConversationBufferMemory

memory = ConversationBufferMemory()

memory.save_context(
    {"input": "Hi!"},
    {"output": "How are you?"}
)

memory.load_memory_variables({})
# {'history': 'Human: Hi!\nAI: How are you?'}
```

아래 code와 같이 `ConversationBufferMemory` 함수에 `return_messages=True`를 추가하면 출력이 다음과 같이 바뀐다.

chat model은 AI message와 Human message가 모두 필요하므로 위 옵션이 유용하다.

```python
from langchain.memory import ConversationBufferMemory

memory = ConversationBufferMemory(return_messages=True)

memory.save_context(
    {"input": "Hi!"},
    {"output": "How are you?"}
)

memory.load_memory_variables({})
# {'history': [HumanMessage(content='Hi!'), AIMessage(content='How are you?')]}
```

## ConversationBufferWindowMemory

[ConversationBufferWindowMemory][conversation-buffer-window]는 대화의 특정 부분만 저장한다.

예를 들어, 최근 5개 message만 저장하게 할 수 있다.

아래 code는 최대 4개 conversation을 저장한다.

전체 내용을 알지 못해 유용한 답변을 얻지 못할 가능성이 존재한다.

```python
from langchain.memory import ConversationBufferWindowMemory

memory = ConversationBufferWindowMemory(
    return_messages=True,
    k=4
)

def add_message(input, output):
    memory.save_context(
        {"input": input},
        {"output": output}
    )

add_message(1, 1)
add_message(2, 2)
add_message(3, 3)
add_message(4, 4)
add_message(5, 5)

memory.load_memory_variables({})
# {'history': [HumanMessage(content='2'),
#   AIMessage(content='2'),
#   HumanMessage(content='3'),
#   AIMessage(content='3'),
#   HumanMessage(content='4'),
#   AIMessage(content='4'),
#   HumanMessage(content='5'),
#   AIMessage(content='5')]}
```

## ConversationSummaryMemory

[ConversationSummaryMemory][conversation-summary]는 LLM을 기반으로 conversation을 요약한 후 저장한다.

그러므로, memory를 사용할 때 비용이 발생한다.

초반에는 conversation을 요약하기 위해 많은 token과 저장 공간을 차지할 수 있지만, conversation이 많아질수록 효율적일 것이다.

[jupyter][jupyter]를 통해 아래 code를 연속으로 실행한다.

```python
from langchain.memory import ConversationSummaryMemory
from langchain.chat_models import ChatOpenAI

llm = ChatOpenAI(temperature=0.1)

memory = ConversationSummaryMemory(llm=llm)

def add_message(input, output):
    memory.save_context({"input": input}, {"output": output})

def get_history():
    return memory.load_memory_variables({})

add_message("Hi I'm troubleshooter, I live in South Korea", "Wow that is so cool!")
```

```python
add_message("South Korea is so pretty", "I wish I could go!!!")
```

```python
get_history()
# {'history': 'The human introduces themselves as troubleshooter from South Korea. The AI responds by expressing admiration for that fact and wishing it could visit because South Korea is so pretty.'}
```

## ConversationSummaryBufferMemory

[ConversationSummaryBufferMemory][conversation-summary-buffer]는 `ConversationSummaryMemory`와 `ConversationBufferMemory`의 결합이다.

memory에 보내온 conversation의 수를 저장하는 것이다.

지정한 limit에 다다르면 오래된 conversion에 대해 요약하여 저장한다.

아래 code에서 `max_token_limit`은 conversation이 요약되기 전 최대 conversion token 수이다.

```python
from langchain.memory import ConversationSummaryBufferMemory
from langchain.chat_models import ChatOpenAI

llm = ChatOpenAI(temperature=0.1)

memory = ConversationSummaryBufferMemory(
    llm=llm,
    max_token_limit=50,
    return_messages=True,
)

def add_message(input, output):
    memory.save_context({"input": input}, {"output": output})

def get_history():
    return memory.load_memory_variables({})

add_message("Hi I'm troubleshooter, I live in South Korea", "Wow that is so cool!")
```

```python
get_history()
# {'history': [HumanMessage(content="Hi I'm troubleshooter, I live in South Korea"),
#   AIMessage(content='Wow that is so cool!')]}
```

```python
add_message("South Korea is so pretty", "I wish I could go!!!")
```

```python
get_history()
# {'history': [SystemMessage(content='The human introduces themselves as troubleshooter and mentions they live in South Korea.'),
#   AIMessage(content='Wow that is so cool!'),
#   HumanMessage(content='South Korea is so pretty'),
#   AIMessage(content='I wish I could go!!!')]}
```

요약된 내용이 SystemMessage로 추가된 것을 확인할 수 있다.

## ConversationKGMemory

[ConversationKGMemory][conversation-knowledge-graph]는 LLM을 이용해서 conversation entity의 knowledge graph를 생성한다.

knowledge graph란 가장 중요한 것만 추출한 요약본이다.

```python
from langchain.memory import ConversationKGMemory
from langchain.chat_models import ChatOpenAI

llm = ChatOpenAI(temperature=0.1)

memory = ConversationKGMemory(
    llm=llm,
    return_messages=True,
)

def add_message(input, output):
    memory.save_context({"input": input}, {"output": output})

add_message("Hi I'm troubleshooter, I live in South Korea", "Wow that is so cool!")
```

```python
memory.load_memory_variables({"input": "who is troubleshooter"})
# {'history': [SystemMessage(content='On troubleshooter: troubleshooter lives in South Korea.')]}
```

```python
add_message("troubleshooter likes kimchi", "Wow that is so cool!")
```

```python
memory.load_memory_variables({"inputs": "what does troubleshooter like"})
# {'history': [SystemMessage(content='On troubleshooter: troubleshooter lives in South Korea. troubleshooter likes kimchi.')]}
```

message를 추가할 때마다 history에 추가되지 않고, SystemMessage가 update된다.

## ConversationTokenBufferMemory

[ConversationTokenBufferMemory][conversation-token-buffer]는 `ConversationBufferWindowMemory`와 달리 interaction의 최대 값 대신 token length를 기반으로 저장한다.

## ConversationEntityMemory

[ConversationEntityMemory][conversation-entity-memory]는 LLM을 이용해서 entity로부터 정보를 추출한다.

## Memory on LLMChain

`LLMChain`이란 `off-the-shelf chain`으로서 일반적 목적을 가지고 이미 만들어진 chain을 의미한다.

`verbose` option은 답변을 생성하는 동안 chain의 prompt를 확인할 수 있다.

아래 code를 실행하면 마지막 question에 대해 답변하지 못한다.

`memory.load_memory_variables({})`를 통해 memory를 확인해보면, 정상적으로 저장되는 것을 확인할 수 있으나, LLM은 memory의 prompt를 알지 못한다.

```python
from langchain.memory import ConversationSummaryBufferMemory
from langchain.chat_models import ChatOpenAI
from langchain.chains import LLMChain
from langchain.prompts import PromptTemplate

llm = ChatOpenAI(temperature=0.1)

memory = ConversationSummaryBufferMemory(
    llm=llm,
    max_token_limit=80
)

chain = LLMChain(
    llm=llm,
    memory=memory,
    prompt=PromptTemplate.from_template("{question}"),
    verbose=True
)

chain.predict(question="My name is Troubleshooter")
```

```python
chain.predict(question="I live in United States")
```

```python
chain.predict(question="What is my name?")
```

위 문제를 해결하기 위해 이전 conversation을 template을 통해 추가한다.

`ConversationSummaryBufferMemory` 함수가 template 내에 conversation을 자동으로 넣기 위해,

`ConversationSummaryBufferMemory` 함수의 `memory_key`와 `template`의 conversation 저장 변수명을 동일하게 한다.

```python
from langchain.memory import ConversationSummaryBufferMemory
from langchain.chat_models import ChatOpenAI
from langchain.chains import LLMChain
from langchain.prompts import PromptTemplate

llm = ChatOpenAI(temperature=0.1)

memory = ConversationSummaryBufferMemory(
    llm=llm,
    max_token_limit=120,
    memory_key="chat_history"
)

template="""
    You are a helpful AI talking to human.

    {chat_history}
    Human: {question}
    You:
"""

chain = LLMChain(
    llm=llm,
    memory=memory,
    prompt=PromptTemplate.from_template(template),
    verbose=True
)

chain.predict(question="My name is Troubleshooter")
```

```python
chain.predict(question="I live in United States")
```

```python
chain.predict(question="What is my name?")
```

`vervose` option을 통해 conversation이 요약되어 추가된 template을 확인할 수 있다.

## Chat Based Memory

`memory.load_memory_variables({})`를 통해 text 형식으로 memory를 확인할 수 있는 반면, `Chat Based Memory`는 대화 기반 채팅으로 사용할 수 있게 한다.

`return_message`는 text가 아닌 Human / AI / System message로 변경한다.

## References

- [ConversationBufferMemory][conversation-buffer]
- [ConversationBufferWindowMemory][conversation-buffer-window]
- [ConversationSummaryMemory][conversation-summary]
- [ConversationKGMemory][conversation-knowledge-graph]
- [ConversationTokenBufferMemory][conversation-token-buffer]

[conversation-buffer]: https://python.langchain.com/docs/modules/memory/types/buffer/
[conversation-buffer-window]: https://python.langchain.com/docs/modules/memory/types/buffer_window/
[conversation-summary]: https://python.langchain.com/docs/modules/memory/types/summary/
[jupyter]: https://jupyter.org
[conversation-summary-buffer]: https://python.langchain.com/docs/modules/memory/types/summary_buffer/
[conversation-knowledge-graph]: https://python.langchain.com/docs/modules/memory/types/kg/
[conversation-token-buffer]: https://python.langchain.com/docs/modules/memory/types/token_buffer/
[conversation-entity-memory]: https://python.langchain.com/docs/modules/memory/types/entity_summary_memory/
