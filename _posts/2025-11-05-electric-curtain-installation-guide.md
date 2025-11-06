---
layout: post
title: "[ST] 전동 커튼 설치 가이드"
date: 2025-11-05 16:09 +0900
categories: [IoT, ST]
tags: [IoT, ST]
image:
  path: /assets/img/2025-11-05-electric-curtain-installation-guide/thumbnail.png
---

`SmartThings` 기반 전동 커튼 설치 가이드를 포스팅한다.

## Ecosystem

거실 창가 전체 면에 설치하고자 아래와 같이 제품을 선정했다. 모터는 5년 전 구매했던 제품을 재활용했다.

- 모터: Aqara ZNCLDJ11LM
  - 해당 제품은 `Zigbee` 통신만 지원하는 제품이며, 출시한 지 오래되었다.
  - `SmartThings`, `Google Home`, `Apple Homekit` 등 다양한 플랫폼을 운용중이라면, `Matter` 지원 모터 구매를 권장한다.
- 커튼 레일: [AliExpress](https://ko.aliexpress.com/item/1005009258861607.html?spm=a2g0o.order_list.order_list_main.51.6579140f2r0iaQ&gatewayAdapt=glo2kor)
  - 반드시 모터 호환성을 확인 후 구매해야 한다.
  - 국내 업체 중 `윈테리어`, `마마바`에 해당 모터 호환 여부를 확인했으나, 불가하다는 답변을 받아 `AliExpress`에서 해당 제품을 선택했다.
- 커튼: [릴리 린넨룩 속커튼 |바닐라|](https://elenaheim.com/shop/shopdetail.html?branduid=3383109)
  - 나비주름형 1폭(60cm)
  - 커튼길이: 220~240cm
  - 커튼 열리는 방식: 양개형(2장으로 나눠제작)
  - 주름 형상기억 가공 추가: 형상기억 가공 추가(+16000)
  - 설치할 공간의 가로cm: 355
  - 원하는 커튼 완성 높이cm: 233

설치 면적에 따라 사이즈 선택이 중요하다. 가로 길이의 경우 커튼 레일 제품 설명의 지침대로 전체 벽면 길이에서 `4cm`를 제외한 `355cm`로 설정했으며, 세로는 `5cm`를 제외한 `233cm`로 설정했다.

현재 제품을 주문한 상태이며, 커튼 제작 시간과 레일 도착 이후 포스팅을 업데이트할 예정이다.
