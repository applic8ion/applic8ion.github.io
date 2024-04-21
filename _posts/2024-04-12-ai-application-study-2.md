---
layout: post
title: "[AI] AI Application Study [2/n]"
date: 2024-04-12 00:27 -0700
categories: [AI, Study]
tags: [AI, GPT, LangChain]
mermaid: true
image:
  path: /assets/img/2024-04-01-ai-application-study/ai_parrot.JPG
---

## LengthBasedExampleSelector

설정에 따라 알맞은 example을 선택하는 기능이다.

example이 많을수록 비용이 증가하므로 사용한다.

`LengthBasedExampleSelector` function에서 `max_length` argument에 따라 `examples` argument 중에서 임의의 example이 선택된다.

`max_length` argument는 시험을 통해 적절한 값을 찾아야 한다.

`LengthBasedExampleSelector`를 이용해서 초기화한 변수는 `FewShotPromptTemplate`의 `examples` argument를 제거하고 `example_selector` argument에 할당하여 사용한다.

이전 `FewShotPromptTemplate` 예제에서는 `example_selector` argument 대신 `examples`를 사용했었다.

[[AI] AI Application Study [1/n]][2024-04-01-ai-application-study-1] 에서 `FewShotPromptTemplate` 예제를 다음과 같이 수정한다.

아래 code는 Brazil이 example에 존재하지 않지만, max_length를 늘려줌으로써 임의의 example이 출력된다.

Runnable interface는 생략했다.

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

`LengthBasedExampleSelector`는 단순히 예제 개수를 설정하는 반면, `BaseExampleSelector`는 특정 logic을 이용하여 example을 선택할 수 있다.

아래 코드는 `BaseExampleSelector`를 상속한 `RandomExampleSelector` class를 통해 임의의 example을 선택한다.

`RandomExampleSelector` class는 예제와 같이 상속받은 함수가 정의되어 있어야 한다.

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

## Serialization

file로부터 prompt를 불러오는 기능이다.

### prompt.json

prompt.json 파일을 생성하고 다음과 같이 작성한다.

```json
{
  "_type": "prompt",
  "template": "What is the capital of {country}",
  "input_variables": ["country"]
}
```

python example code는 다음과 같다.

```python
from langchain.chat_models import ChatOpenAI
from langchain.callbacks import StreamingStdOutCallbackHandler
from langchain.prompts import load_prompt

prompt = load_prompt("./prompt.json")
prompt.format(country="Germany")
```

### prompt.yaml

prompt.yaml 파일을 생성하고 다음과 같이 작성한다.

json 보다 단순해 보일뿐 기능은 동일하다.

```yaml
_type: "prompt"
template: "What is the capital of {country}"
input_variables: ["country"]
```

python example code는 다음과 같다.

```python
from langchain.chat_models import ChatOpenAI
from langchain.callbacks import StreamingStdOutCallbackHandler
from langchain.prompts import load_prompt

prompt = load_prompt("./prompt.yaml")
prompt.format(country="Germany")
```

## PipelinePromptTemplate

여러 개의 prompt들을 하나로 합치는 기능이다.

예제에서 3개는 작은 prompt이고 나머지 1개는 모든 것을 포함하는 prompt이다.

- intro: `{character}`를 흉내내는 AI라고 알려주는 내용
- example: character가 대답할 예제
- start: AI가 intro와 example을 기반으로 실제 질문할 text를 완성해주는 예제
- final: 위 모든 것들을 하나로 합친 prompt로서 `PipelinePromptTemplate` 함수의 `final_prompt` 매개변수로 입력

```python
from langchain.chat_models import ChatOpenAI
from langchain.callbacks import StreamingStdOutCallbackHandler
from langchain.prompts import PromptTemplate
from langchain.prompts.pipeline import PipelinePromptTemplate

chat = ChatOpenAI(
    temperature=0.1,
    streaming=True, # Check the chatmodel response creation process
    callbacks=[StreamingStdOutCallbackHandler()]
)

intro = PromptTemplate.from_template(
    """
    You are a role playing assistant.
    And you are impersonating a {character}
"""
)

example = PromptTemplate.from_template(
    """
    This is an example of how you talk:

    Human: {example_question}
    You: {example_answer}
"""
)

start = PromptTemplate.from_template(
    """
    Start now!

    Human: {question}
    You:
"""
)

final = PromptTemplate.from_template(
    """
    {intro}

    {example}

    {start}
"""
)

prompts = [
    ("intro", intro),
    ("example", example),
    ("start", start),
]

full_prompt = PipelinePromptTemplate(
    final_prompt=final,
    pipeline_prompts=prompts,
)

# Check completed prompts
# full_prompt.format(
#     character="Pirate",
#     example_question="What is your location?",
#     example_answer="Arrrrrg! That is a secret!! Arg arg!!",
#     question="What is your fav food?"
# )

chain = full_prompt | chat

chain.invoke({
    "character": "Pirate",
    "example_question": "What is your location?",
    "example_answer": "Arrrrrg! That is a secret!! Arg arg!!",
    "question": "What is your fav food?"
})
# Arrr matey! Me favorite grub be a hearty plate of salted beef and hardtack! Aye, nothing beats the taste of a good ol' pirate feast on the high seas! Arrrr!
```

## Caching

`LM (Language Model)`의 답변을 저장함으로써, 이미 답변한 내용을 재사용할 수 있다.

아래 예제에서 `set_llm_cache(InMemoryCache())`는 memory에 답변을 저장한다.

답변을 얻는데 걸리는 시간을 확인하기 위해 streaming option을 껐다.

`set_debug(True)` 옵션은 답변 생성하는 동안 로그를 표시한다.

```python
from langchain.chat_models import ChatOpenAI
from langchain.callbacks import StreamingStdOutCallbackHandler
from langchain.globals import set_llm_cache, set_debug
from langchain.cache import InMemoryCache

set_llm_cache(InMemoryCache())
set_debug(True)

chat = ChatOpenAI(
    temperature=0.1,
    # streaming=True, # Check the chatmodel response creation process
    # callbacks=[StreamingStdOutCallbackHandler()]
)

chat.predict("How do you make Italian pasta")
# '1. Start by bringing a large pot of salted water to a boil.\n\n2. Add your choice of pasta to the boiling water and cook according to package instructions until al dente.\n\n3. While the pasta is cooking, heat a large skillet over medium heat and add olive oil.\n\n4. Add minced garlic and cook until fragrant, about 1-2 minutes.\n\n5. Add your choice of vegetables, such as cherry tomatoes, zucchini, bell peppers, or mushrooms, and cook until they are tender.\n\n6. Add cooked protein, such as grilled chicken, shrimp, or Italian sausage, if desired.\n\n7. Drain the cooked pasta and add it to the skillet with the vegetables and protein.\n\n8. Toss everything together and season with salt, pepper, and any desired herbs or spices, such as basil, oregano, or red pepper flakes.\n\n9. Serve the pasta hot, topped with grated Parmesan cheese and a drizzle of olive oil, if desired.\n\n10. Enjoy your delicious homemade Italian pasta!'
```

처음 위 답변을 얻는데 3.7s 걸렸다.

[jupyter][jupyter]를 이용하여 위 예제 아래에 `chat.predict("How do you make Italian pasta")`을 실행하면 0.0s 걸린다.

아래 예제와 같이 `InMemoryCache` 대신 `SQLiteCache`를 이용하면 SQLite 형식 파일로 답변을 저장할 수 있다.

```python
from langchain.chat_models import ChatOpenAI
from langchain.callbacks import StreamingStdOutCallbackHandler
from langchain.globals import set_llm_cache
from langchain.cache import SQLiteCache

set_llm_cache(SQLiteCache("cache.db"))

chat = ChatOpenAI(
    temperature=0.1,
    # streaming=True, # Check the chatmodel response creation process
    # callbacks=[StreamingStdOutCallbackHandler()]
)

chat.predict("How do you make Italian pasta")
```

[Integration][langchain-integrations]을 통해 third-party components를 확인할 수 있다.

[LLM Caching integrations][llm_caching]에서 다양한 caching 방법을 확인할 수 있다.

## get_openai_callback

ChatGPT 사용 내역 확인하는 방법이다.

아래 예제는 실제 답변을 출력하지는 않는다.

```python
from langchain.chat_models import ChatOpenAI
from langchain.callbacks import get_openai_callback


chat = ChatOpenAI(
    temperature=0.1,
)

with get_openai_callback() as usage:
    chat.predict("What is the recipe for soju")
    print(usage)
# Tokens Used: 204
# 	Prompt Tokens: 14
# 	Completion Tokens: 190
# Successful Requests: 1
# Total Cost (USD): $0.00040100000000000004
```

아래 예제를 실행하면 답변과 사용 내역을 모두 출력할 수 있다. (답변 생략)

```python
from langchain.chat_models import ChatOpenAI
from langchain.callbacks import get_openai_callback


chat = ChatOpenAI(
    temperature=0.1,
)

with get_openai_callback() as usage:
    a = chat.predict("What is the recipe for soju")
    b = chat.predict("What is the recipe for bread")
    print(a, b, "\n")
    print(usage)
```

`usage.total_cost`, `usage.total_token`, `usage.prompt_tokens`, `usage.completion_tokens`를 통해 부분적으로 확인할 수도 있다.

## Model Serialization

다음 예제를 통해 `OpenAI` LLM 설정을 저장할 수 있다.

```python
from langchain.llms.openai import OpenAI

chat = OpenAI(
    temperature=0.1,
    max_tokens=450,
    model="gpt-3.5-turbo-instruct"
)

chat.save("model.json")
# {
#     "model_name": "gpt-3.5-turbo-instruct",
#     "temperature": 0.1,
#     "max_tokens": 450,
#     "top_p": 1,
#     "frequency_penalty": 0,
#     "presence_penalty": 0,
#     "n": 1,
#     "request_timeout": null,
#     "logit_bias": {},
#     "_type": "openai"
# }
```

다음 code를 통해 저장한 LLM을 불러올 수 있다.

결과에서는 `API Key`가 포함되므로 출력 결과는 생략한다.

```python
from langchain.llms.loading import load_llm

chat = load_llm("model.json")

chat
```

## References

- [[AI] AI Application Study [1/n]][2024-04-01-ai-application-study-1]
- [jupyter][jupyter]
- [Integration][langchain-integrations]
- [LLM Caching integrations][llm_caching]

[2024-04-01-ai-application-study-1]: https://applic8ion.github.io/posts/ai-application-study-1/
[jupyter]: https://jupyter.org
[langchain-integrations]: https://python.langchain.com/docs/integrations/platforms/
[llm_caching]: https://python.langchain.com/docs/integrations/llms/llm_caching/
