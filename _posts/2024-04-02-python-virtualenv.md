---
layout: post
title: "[Python] virtualenv"
date: 2024-04-02 00:36 -0700
categories: [Language, Python]
tags: [Python, virtualenv]
image:
  path: /assets/img/2024-04-02-python-virtualenv/python_virtualenv.png
---

## virtualenv

`Python` 설치 후 다양한 module을 설치하다보면 특정 program 배포 시, dependant module 식별이 어려울 수 있다.

이를 해결하기 위해 program 최초 작성 시, [virtualenv][python-venv]를 항상 사용하여 작성하는 것이 좋다.

## Installation

### macOS

```bash
python3 -m venv [env name]
```

`[env name]` 부분에 `virtualenv`의 이름을 작성한다.

## Activation

### macOS

```bash
source [env name]/bin/activate
```

## Module Installation from requirements.txt

```bash
pip install -r requirements.txt
```

[python-venv]: https://docs.python.org/3/library/venv.html
