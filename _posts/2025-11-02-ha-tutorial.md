---
layout: post
title: "[HA] Home Assistant Tutorial"
date: 2025-10-29 00:52 +0900
categories: [IoT, HA]
tags: [IoT, HA]
image:
  path: /assets/img/2025-11-02-ha-tutorial/thumbnail.png
---

`Docker` 환경에서 [HA(Home Assistant)](https://www.home-assistant.io) 설치 및 기본 사용법을 포스팅한다.

## 설치 및 실행

`macOS`에서 `Colima`를 통해 `Docker` 가상환경을 구성했다. Colima 설정 방법은 [링크](https://applic8ion.github.io/posts/docker-for-macos/)를 참고한다. `Ubuntu`와 같은 기존 리눅스 환경 사용자는 `Docker`가 사용 가능한 환경을 구성한다.

아래 명령어를 통해 HA 이미지를 Pull 한 후 실행한다.

```bash
docker run -d \
  --name homeassistant \
  --privileged \
  --restart=unless-stopped \
  -e TZ=MY_TIME_ZONE \
  # MY_TIME_ZONE: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones 에서 코드 확인
  -v /PATH_TO_YOUR_CONFIG:/config \
  # /PATH_TO_YOUR_CONFIG: HA 설정 파일 등을 저장하기 위한 로컬 경로
  -v /run/dbus:/run/dbus:ro \
  --network=host \
  ghcr.io/home-assistant/home-assistant:stable
```

나는 아래와 같이 입력했다.

```bash
docker run -d --name homeassistant --privileged --restart=unless-stopped -e TZ=KR -v ~/HA:/config -v /run/dbus:/run/dbus:ro --network=host ghcr.io/home-assistant/home-assistant:stable
```

## 기본 사용

[http://localhost:8123](http://localhost:8123)에 접속한다.

[Onboarding Home Assistant](https://www.home-assistant.io/getting-started/onboarding/)를 참고하여 초기 설정을 완료한다.

설정이 완료되면 [http://localhost:8123/onboarding.html](http://localhost:8123/onboarding.html) 접속이 불가한 상태가 되는데, 다시 [http://localhost:8123](http://localhost:8123)에 접속하면 로그인 화면이 표시되고, 로그인하면 `Dashboards`에 접근할 수 있다.

### Home Assistant Application

iPhone `Home Assistant` 앱을 실행하고 최초 접속 시, 자동으로 발견되는 URL로 설정하면 접속이 불가능했다. 서버를 실행중인 macOS의 IP 주소와 달랐다는 것을 확인하고 직접 수동으로 설정했다.
