---
layout: post
title: "[Thread] Thread Network Integration"
date: 2025-11-03 01:56 +0900
categories: [IoT, Thread]
tags: [IoT, Thread]
image:
  path: /assets/img/2025-11-03-thread-network-integration/thumbnail.png
---

다양한 스마트홈 플랫폼이 구축된 환경에서 Thread Network를 통합하기 위한 방법을 포스팅한다.

## Thread Network 통합

`Thread Network`를 통합한다고 해서 모든 플랫폼이 통합 관리되는 것은 아니다. `Matter`를 지원하는 기기를 활용해야만 통합 관리가 가능하다.

`Thread Network`는 핸드폰 OS에 따라 특정 스마트홈 플랫폼의 Credential을 전송할 수 있다. Android의 경우 `Google`, iOS의 경우 `Apple` 플랫폼의 Credential을 전송할 수 있다. [Thread](https://www.home-assistant.io/integrations/thread/)에 설명되어 있다.

`Thread Network` 통합은 [스마트싱스와 구글 Nest : Thread 네트워크 통합 가이드](https://cafe.naver.com/stsmarthome/106690)를 참고했다.

### Ecosystem

`Apple TV 4K`, `Home Pod 2nd Gen`, `SmartThings Hub V3`, `Google Net Hub 2nd Gen`을 보유중이며, `iPhone`을 사용중이다.

### Procedure

이 방법은 위 Echo System에서 `Google`의 `Thread Network`로 통합하는 절차를 설명한다. `SmartThings Hub V3`는 연결된 기기를 모두 삭제해야 한다.

1. iPhone `Home Assistant` 앱을 실행하고 HA 서버에 접속한다.

2. 접속되면, `Settings > Devices & services`로 이동한 다음, `Thread`를 선택하고 톱니바퀴 아이콘을 선택한다.

3. 처음에는 `Preferred Network`에 아무것도 표시되지 않고, `Other Networks`에 `Google`, `Apple` 로고가 보이고, `SmartThings`는 로고가 보이지 않고 `ST-`로 시작하는 Thread Network ID를 갖는다.

4. `Send credentials to Home Assistant`를 누르면 `Apple`이 `Google`의 `NEST-`로 시작하는 Thread Network ID에 가입된다.

5. `SmartThings` 앱을 실행하고 등록된 `SmartThings Hub`를 선택하고 우측 상단 `... > 설정`을 선택한다.

6. `Thread 네트워크 관리`를 선택한다.

7. `Thread 네트워크 통합 > 이 허브가 다른 서비스의 Thread 네트워크에 참여` 선택한다.

8. `NEST-`로 시작하는 Thread Network가 표시될 것이며, 해당 네트워크에 통합한다.

9. `Home Assistant` 앱과 `SmartThings` 앱을 통해 아래와 같이 통합이 완료된 것을 확인할 수 있다.

![Desktop View](/assets/img/2025-11-03-thread-network-integration/thread-network-integration-st.png){: .shadow .rounded-10 w="500" }
_SmartThings에서 Thread Network 확인_

![Desktop View](/assets/img/2025-11-03-thread-network-integration/thread-network-integration-ha.png){: .shadow .rounded-10 w="500" }
_Home Assistant에서 Thread Network 확인_
