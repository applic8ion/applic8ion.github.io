---
layout: post
title: "[ST] TRADFRI 5-Button Remote Control Pairing"
date: 2025-11-03 02:12 +0900
categories: [IoT, ST]
tags: [IoT, SmartThings, IKEA]
image:
  path: /assets/img/2025-11-03-tradfri-remote-control-pairing/thumbnail.png
---

`IKEA`의 `TRÅDFRI` 5-Button Remote Control을 `SmartThings`에 연결하는 방법을 포스팅한다.

## Ecosystem

`SmartThings Hub V3`와 `iPhone`을 사용했다.

## Issues

1. `SmartThings` 앱에서 `기기추가 > 제휴사 기기 > IKEA > 리모컨/버튼 > IKEA Button`을 선택한 후, `Pairing` 버튼을 4회 눌러도 `TRÅDFRI가 인식조차 되지 않았다.

2. `SmartThings` 앱에서 `기기추가 > 주변 검색`을 선택한 후, `Pairing` 버튼을 4회 누르면 기기 등록이 가능했으나, 버튼을 눌러도 인식되지 않았다.

## 해결 방법

1. `SmartThings` 앱에서 `TRÅDFRI` 기기를 삭제한다.

2. [초대링크](https://bestow-regional.api.smartthings.com/invite/Kr2zNDg0Wr2A)를 통해 드라이버를 `SmartThings Hub`에 설치한다.

3. `Available Drivers`를 선택하고, `Ikea Button Tweaks`를 `Install` 버튼을 눌러 설치한다.

4. [Issues](https://applic8ion.github.io/posts/tradfri-remote-control-pairing/#issues)에서 2번 방법으로 `TRÅDFRI`를 등록한다.

5. [SmartThings Advanced](https://my.smartthings.com/advanced)에 접속하여 로그인하고, `Devices` 탭으로 이동한다.

6. 등록했던 `TRÅDFRI`를 선택하고, `Driver` 부분을 `Ikea Button Tweaks`으로 변경한다.

7. 정상적으로 작동하는지 테스트한다.

## Google Home 연동

`Google Home`에서는 `앱 또는 서비스 연결` 메뉴를 통해 쉽게 `SmartThings`에 등록된 기기를 추가할 수 있지만, `TRÅDFRI`는 자동으로 추가되지 않는 문제가 있었다.

이를 해결하기 위해 [스크립트 편집기로 고급 홈 자동화 만들기](https://support.google.com/googlenest/answer/13323253?hl=ko)를 참고했으며, 현 시점에서는 웹용 `Google Home`으로만 설정 가능하다.

1. [SmartThings Advanced](https://my.smartthings.com/advanced)의 `Devices > +Add Device`를 선택하여 `Virtual Switch`를 5개 추가한다.

- Device Name: 버튼 별로 Top, Bottom, Left, Right, Power 문구 포함
- Execution Location: Local
- Hub: 등록된 SmartThings Hub
- Device Type: Switch

2. `SmartThings` 앱에서 `TRÅDFRI`의 물리 버튼을 각 `Virtual Switch`가 켜지거나 꺼지도록 매핑한다.

3. [웹용 Google Home](https://home.google.com)에 접속한 후, `자동화` 탭을 선택한다.

4. `+ 새로 추가` 버튼을 클릭하여 스크립트를 작성한다. 아래 예시는 거실에 위치한 `TRÅDFRI`의 `Bottom` 버튼을 누르면 2개의 주방 조명이 순차적으로 켜지는 스크립트이다.

```yaml
# ------------------------------------------------------------------------------------------ #
# Lines starting with “#” are comments and will be ignored by the automation.
# Indentation determines hierarchy within the script.
# Visit g.co/home/script-editor-docs for full documentation.
# ------------------------------------------------------------------------------------------ #

metadata:
  name: Turn TRADFRI Bottom On # Choose a short name that summarizes important starters and actions, like “Sunset lights”.
  description: Basic example of turning on a light with another switch. # Write a detailed description that includes everything the automation does, like “At sunset, on weekdays, close blinds, turn on lights to 50%, and play the sunset playlist on certain speakers.”

automations:
  # “starters” and “actions” are required; “conditions” are optional.
  # Use Ctrl + Space to see autocomplete suggestions.

  # ---- STARTERS ---- #
  # Starters describe events that will start the automation.
  # To add more than one starter, duplicate the "- type" section under "starters".
  # If you add multiple starter events, any one of them happening will start the automation.
  starters:
    - type: device.state.OnOff # The basic on and off functionality for any device that has binary on and off, including plugs and switches as well as many future devices.
      state: on
      # Whether a device with an on/off switch is on or off. <code>true</code> if the device is on, <code>false</code> if the device is off. [available operators: is, isNot]
      is: true
      device: TRADFRI Bottom - 거실

  # ---- ACTIONS ---- #
  # Actions will start when a starter event takes place and all conditions are met.
  # Actions will start in the order they appear in the script and with any delay specified.
  # To add more than one action, duplicate the "- type" section under "actions".
  actions:
    - type: device.command.OnOff # Turn the device on or off.
      # Whether to turn the device on or off.
      on: true
      devices:
        - 주방1 Light - 주방
    - type: time.delay
      for: 1sec
    - type: device.command.OnOff
      devices:
        - 주방2 Light - 주방
      on: true
```
