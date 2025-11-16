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

거실창 벽면에 설치하고자 아래와 같이 제품을 선정했다. 모터는 5년 전 구매했던 제품을 재활용했다.

### 설치 환경

- 거실창을 모두 덮을 수 있는 양개형 커튼 설치
- 가로: `358cm`
- 세로: `238cm`

### 모터 (Aqara ZNCLDJ11LM)

![Desktop View](/assets/img/2025-11-05-electric-curtain-installation-guide/curtain-motor.jpeg){: .shadow .rounded-10 w="500" }
_Aqara ZNCLDJ11LM_

- 해당 제품은 `Zigbee` 통신만 지원하는 제품이며, 단종된 제품이다.
- 예전에 사용했던 제품을 재사용하는 것이며, 국내 전원 규격을 따르기 때문에 220V 콘센트와 전선을 적절한 길이로 연결해주었다.
- `SmartThings`, `Google Home`, `Apple Homekit` 등 다양한 플랫폼을 운용중이라면, `Matter` 지원 모터 구매를 권장한다.

### [SmartThings V3 Hub](https://www.samsung.com/nz/smartthings/hub/smartthings-v3-hub-gp-u999sjvlgaa/?srsltid=AfmBOoqPdYT8tTWMOpUAqvKWosEEw7ztVlP88TFb43wmtfag3DUpeNyA)

<img src="/assets/img/2025-11-05-electric-curtain-installation-guide/smartthings-hub-v3.jpeg" alt="Desktop View" class="shadow rounded-10" style="width: 100%; max-width: 900px;">
_SmartThings V3 Hub_

- `Zigbee` 통신만 지원하는 모터를 연결하기 위해 필수적으로 요구된다.
- 현 시점 단종되었으며, 신형 허브가 계속 출시되고 있다.

### 커튼 레일 ([AliExpress][aliexpress-curtain-rail])

<div style="display: flex; gap: 16px; flex-wrap: wrap;">
  <div style="flex: 1; min-width: 220px; text-align: center;">
    <img src="/assets/img/2025-11-05-electric-curtain-installation-guide/rail.jpeg" alt="Payment Summary" class="shadow rounded-10" width="100%" style="max-width: 500px;">
    <div style="margin-top: 8px; color: #888;">Curtain Rail</div>
  </div>
  <div style="flex: 1; min-width: 220px; text-align: center;">
    <img src="/assets/img/2025-11-05-electric-curtain-installation-guide/rail-components.jpeg" alt="Payment Received" class="shadow rounded-10" width="100%" style="max-width: 500px;">
    <div style="margin-top: 8px; color: #888;">Components for Curtain Rail</div>
  </div>
</div>

- 옵션:
  - `For aqara ZNCLDJ11LM`
  - `3.6m or less`
- 판매자에게 정확한 길이를 알려줘야 하는데 깜빡했다. 아래에 자세히 설명한다
- 국내 업체 중 [윈테리어](https://winterior.kr), [마마바](https://mamaba.co.kr)에 해당 모터 호환 여부를 확인했보니, 대부분 `Aqara B1`부터 지원하며, `ZNCLDJ11LM`는 불가하다는 답변을 받아 `AliExpress`에서 해당 제품을 선택했다.
- 최신형은 `Matter`를 지원하므로, 새로 구매할 경우, 반드시 해당 제품으로 구매하는 것을 권장한다.
- 각 부분품은 아래 `Accessories for Curtain Rail` 이미지를 참고한다.

![Desktop View](/assets/img/2025-11-05-electric-curtain-installation-guide/accessories-for-curtain-rail.jpg){: .shadow .rounded-10 w="500" }
_Accessories for Curtain Rail_

### 커튼 ([릴리 린넨룩 속커튼 바닐라](https://elenaheim.com/shop/shopdetail.html?branduid=3383109))

![Desktop View](/assets/img/2025-11-05-electric-curtain-installation-guide/curtain.jpg){: .shadow .rounded-10 w="500" }
_릴리 린넨룩 속커튼 바닐라_

- 옵션:

  - `나비주름형 1폭(60cm)`
  - 커튼길이: `220~240cm`
  - 커튼 열리는 방식: 양개형(2장으로 나눠제작)
  - 주름 형상기억 가공 추가: 형상기억 가공 추가(+16000)
  - 설치할 공간의 가로cm: `355`
  - 원하는 커튼 완성 높이cm: `233`

- 설치 면적에 따라 사이즈 선택이 중요하다. 가로 길이의 경우 커튼 레일 제품 설명의 지침대로 전체 벽면 길이에서 `3cm`를 뺀 `355cm`로 설정했으며, 세로는 `5cm`를 뺀 `233cm`로 설정했다.
- 예전에 암막 커튼을 구매했던 적이 있는데, 퀄리티가 괜찮고, 세탁이 된 상태로 배송되는 것 같아, 같은 업체에서 구매했다.
- 주문 제작이므로, 배송 기간이 소요된다.

## 설치 방법

### 1. Curtain Rail의 Connection strip 연결

- [Curtain Rail](#커튼-레일-aliexpress)은 3등분되어 배송되었는데, `Connection Strip`에 장착되어 있는 4개의 볼트를 풀고, Curtain Rail을 일직선으로 배치한 후 다시 체결한다.

<div style="display: flex; gap: 16px; flex-wrap: wrap;">
  <div style="flex: 1; min-width: 220px; text-align: center;">
    <img src="/assets/img/2025-11-05-electric-curtain-installation-guide/connection-strip-1.jpeg" alt="Payment Summary" class="shadow rounded-10" width="100%" style="max-width: 500px;">
    <div style="margin-top: 8px; color: #888;">Curtain Rail</div>
  </div>
  <div style="flex: 1; min-width: 220px; text-align: center;">
    <img src="/assets/img/2025-11-05-electric-curtain-installation-guide/connection-strip-2.jpeg" alt="Payment Received" class="shadow rounded-10" width="100%" style="max-width: 500px;">
    <div style="margin-top: 8px; color: #888;">Accessories for Curtain Rail</div>
  </div>
</div>

### 2. Curtain Rail 재단 (생략 가능)

- `Connection Strip`을 조립한 [Curtain Rail](#커튼-레일-aliexpress)을 커튼 박스에 맞춰보려고 했는데, 길이가 너무 길다는 것을 깨달았다.
- [AliExpress][aliexpress-curtain-rail]에서 주문한 후, 구매자에게 거실창 벽변 가로 길이 `358cm`에서 `3cm`를 뺀 `355cm` 로 제작을 요청해야 했는데, 이를 깜빡해서 `Drive Box`를 포함한 전체 길이가 `360cm`로 배송되었다.
- 당황스러웠지만, 모터를 설치하지 않는 위치의 `Drive Box`를 열어보니, `Belt`가 회전하는 부분을 제외한 플라스틱 부분을 잘라내면 공간 확보가 가능할 것으로 예상되었다.

<div style="display: flex; gap: 16px; flex-wrap: wrap;">
  <div style="flex: 1; min-width: 220px; text-align: center;">
    <img src="/assets/img/2025-11-05-electric-curtain-installation-guide/drive-box-before-1.jpeg" alt="Payment Summary" class="shadow rounded-10" width="100%" style="max-width: 500px;">
    <div style="margin-top: 8px; color: #888;">Drive Box before cutting (a)</div>
  </div>
  <div style="flex: 1; min-width: 220px; text-align: center;">
    <img src="/assets/img/2025-11-05-electric-curtain-installation-guide/drive-box-before-2.jpeg" alt="Payment Received" class="shadow rounded-10" width="100%" style="max-width: 500px;">
    <div style="margin-top: 8px; color: #888;">Drive Box before cutting (b)</div>
  </div>
</div>

- 줄톱으로 플라스틱 부분을 잘라내고, 쇠로 된 부품은 제거하고 조립한 후 테이프로 고정했다.

<div style="display: flex; gap: 16px; flex-wrap: wrap;">
  <div style="flex: 1; min-width: 220px; text-align: center;">
    <img src="/assets/img/2025-11-05-electric-curtain-installation-guide/drive-box-after-1.jpeg" alt="Payment Summary" class="shadow rounded-10" width="100%" style="max-width: 500px;">
    <div style="margin-top: 8px; color: #888;">Drive Box after cutting (a)</div>
  </div>
  <div style="flex: 1; min-width: 220px; text-align: center;">
    <img src="/assets/img/2025-11-05-electric-curtain-installation-guide/drive-box-after-2.jpeg" alt="Payment Received" class="shadow rounded-10" width="100%" style="max-width: 500px;">
    <div style="margin-top: 8px; color: #888;">Drive Box after cutting (b)</div>
  </div>
</div>

<div style="display: flex; gap: 16px; flex-wrap: wrap;">
  <div style="flex: 1; min-width: 220px; text-align: center;">
    <img src="/assets/img/2025-11-05-electric-curtain-installation-guide/drive-box-after-3.jpeg" alt="Payment Summary" class="shadow rounded-10" width="100%" style="max-width: 500px; height: 300px; object-fit: cover;">
    <div style="margin-top: 8px; color: #888;">Drive Box after cutting (c)</div>
  </div>
  <div style="flex: 1; min-width: 220px; text-align: center;">
    <img src="/assets/img/2025-11-05-electric-curtain-installation-guide/drive-box-after-4.jpeg" alt="Payment Received" class="shadow rounded-10" width="100%" style="max-width: 500px; height: 300px; object-fit: cover;">
    <div style="margin-top: 8px; color: #888;">Drive Box after cutting (d)</div>
  </div>
</div>

### 3. Slip Parts 조립

- 양개형이므로 `Slip Parts` `2개`를 조립한다. 서로 엇갈리는 방향으로 조립해야 커튼이 닫혔을 때 가운데 부분을 빈틈없이 가릴 수 있다.
  - 조립 방향은 [초기화](#7-smartthings-hub-연결-및-초기화)를 참고한다.

![Desktop View](/assets/img/2025-11-05-electric-curtain-installation-guide/slip-parts-1.jpeg){: .shadow .rounded-10 w="500" }
_Slip Parts_

### 4. Ceiling Brackets 및 Lock 조립

- `Curtain Rail`을 천장에 고정하기 위한 부품으로서 총 `8개`가 들어있었다. `Ceiling Brackets`을 고정시킨다. 모터가 무겁기 때문에, `2개`는 양쪽 `Drive Box`에 가깝게 배치하고, 나머지 `4개`를 균정하게 배치했다. 천장에 구명을 여러 개 뚫어야 하므로 남은 `2개`는 사용하지 않았다.
- `Drive Box` 바로 옆에 위치한 `Lock`이 창문과 반대 방향을 바라보도록 조절한다.

![Desktop View](/assets/img/2025-11-05-electric-curtain-installation-guide/ceiling-bracket.jpeg){: .shadow .rounded-10 w="500" }
_Ceiling Bracket near the Drive Box_

### 5. Curtain Rail 천장 고정

- 조립한 `Curtain Rail`을 천장에 고정한다. 커튼 박스가 `16cm` 이상 공간이 있으면 2열로 설치가 가능하지만, 우리집은 `14cm`이므로, 커튼 박스의 중앙에 배치하여 설치했다.
- 가운데 `Ceiling Brackets`를 우선 고정한 후 나머지를 고정했다.

<div style="display: flex; gap: 16px; flex-wrap: wrap;">
  <div style="flex: 1; min-width: 220px; text-align: center;">
    <img src="/assets/img/2025-11-05-electric-curtain-installation-guide/curtain-rail-on-ceiling-1.jpeg" alt="Payment Summary" class="shadow rounded-10" width="100%" style="max-width: 500px;">
    <div style="margin-top: 8px; color: #888;">Curtain Rail on ceiling (a)</div>
  </div>
  <div style="flex: 1; min-width: 220px; text-align: center;">
    <img src="/assets/img/2025-11-05-electric-curtain-installation-guide/curtain-rail-on-ceiling-2.jpeg" alt="Payment Received" class="shadow rounded-10" width="100%" style="max-width: 500px;">
    <div style="margin-top: 8px; color: #888;">Curtain Rail on ceiling (b)</div>
  </div>
</div>

### 6. 모터 설치

- 모터를 장착한 후, 전원에 연결한다.

![Desktop View](/assets/img/2025-11-05-electric-curtain-installation-guide/installed-mortor.jpeg){: .shadow .rounded-10 w="500" }
_Installed Mortor_

### 7. SmartThings Hub 연결 및 초기화

- [모터](#모터-aqara-zncldj11lm) 아래 버튼을 길게 눌러서 페어링 모드에 진입한다.
- `SmartThings` 앱에서, `기기 추가 > 주변 검색`을 통해 모터를 추가한다.
- 커튼이 열리고 닫힌 정도(0-100%)를 인지하려면 초기화가 반드시 필요하다. `Slip Parts for initialization` 이미지와 같이 양쪽 `Slip Parts`가 엇갈려서 만나도록 배치한다.
- `SmartThings` 앱에서 앞서 추가한 모터를 선택하고 `Motor initialization` 이미지와 같이 `초기화 > 초기화 시작`을 통해 모터를 초기화한다.
- 잘 열리고 닫히는 지 테스트한다.

![Desktop View](/assets/img/2025-11-05-electric-curtain-installation-guide/slip-parts-2.jpeg){: .shadow .rounded-10 w="500" }
_Slip Parts for initialization_

![Desktop View](/assets/img/2025-11-05-electric-curtain-installation-guide/init-motor.png){: .shadow .rounded-10 w="500" }
_Motor initialization_

[aliexpress-curtain-rail]: https://ko.aliexpress.com/item/1005009258861607.html?spm=a2g0o.order_list.order_list_main.51.6579140f2r0iaQ&gatewayAdapt=glo2kor
