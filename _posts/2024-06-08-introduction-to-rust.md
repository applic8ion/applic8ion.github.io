---
layout: post
title: "[Rust] Introduction to Rust"
date: 2024-06-08 21:31 -0700
categories: [Language, Rust]
tags: [Rust]
image:
  path: /assets/img/2024-06-08-introduction-to-rust/intruduction-to-rust.jpeg
---

## Why Rust

[Rust][rust]는 [Python][python]에 이은 효율적인 programming language 물결이라고 생각한다. Script language인 `Python`과 달리 Compile language이며, 아래 특징이 있다.

- `C`, `C++` 수준의 high performance
- memory 안전성 보장
- compile error 시, debugging 방법 제공
- `Python`의 [PYPI][pypi]가 있다면, `Rust`는 [Crate][crates]를 통해 library 제공

`Rust`의 존재는 알고 있었지만, 고성능 program이나 `embedded` market에서는 `C`, `C++`를 주로 사용중이고, 앞으로도 그럴 것이라고 생각했다.

하지만 [Web Assembly][web-assembly]를 사용해보고자 Rust에 입문하게 되었다.

`C`, `C++` 기반 `Web Assembly`를 사용해도 되지만, compiler인 [Emscripten][emscripten]에 대해 많은 공부가 필요했다.

`Web Assembly`에 대해서는 다음에 다루도록 한다.

[link][rust-lang] 내용을 기반으로 요약했다.

## Install Rust

[link][install-rust]에 접속하여 `platform`에 따라 command line tool인 `rustup`을 설치한다.

### Windows

`rustup-init.exe` 파일을 다운받아 설치한다.

### macOS, Linux

```console
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

## Check Installation

아래 명령어로 설치 여부를 확인한다.

```console
rustc --version
```

## Updating Rust

```console
rustup update
```

## Uninstalling Rust

```console
rustup self uninstall
```

다음에 더 자세히 다루도록 하자.

[rust]: https://www.rust-lang.org
[python]: https://www.python.org
[pypi]: https://pypi.org
[crates]: https://crates.io
[web-assembly]: https://webassembly.org
[emscripten]: https://emscripten.org
[install-rust]: https://www.rust-lang.org/tools/install
[rust-lang]: https://doc.rust-lang.org/book/title-page.html
