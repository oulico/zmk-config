---
type: Reference
title: nRF52840 / Nice!Nano 핀 배치
description: nRF52840 GPIO와 Pro Micro 풋프린트 핀 간 매핑, AliExpress 클론 사용 시 주의사항
tags: [nrf52840, nicenano, pro_micro, gpio, aliexpress-clone]
timestamp: 2026-07-05
---

## Pro Micro 풋프린트 → nRF52840 GPIO 매핑

ZMK의 Nice!Nano 보드 정의(`nice_nano_v2`)에서 pro_micro 커넥터 alias는 아래와 같이 nRF52840 GPIO에 매핑된다.

| pro_micro # | nRF52840 GPIO | 비고 |
|------------|---------------|------|
| 0 | P0.08 | |
| 1 | P0.06 | |
| 2 | P0.15 | |
| 3 | P0.17 | |
| 4 | P0.20 | |
| 5 | P0.22 | |
| 6 | P0.24 | |
| 7 | P1.00 | |
| 8 | P0.11 | |
| 9 | P1.04 | |
| 10 | P1.06 | |
| 14 | P0.25 | MISO |
| 15 | P0.13 | SCK |
| 16 | P0.12 | MOSI |
| 18 | P1.13 | A0 |
| 19 | P1.15 | A1 |
| 20 | P0.31 | A2 |
| 21 | P0.29 | A3 |

## AliExpress 클론 사용 시 주의사항

이 프로젝트에서는 Nice!Nano v2 호환 AliExpress 클론을 사용했다 (좌우 동일 모델).

- **핀 배치는 표준 Nice!Nano v2와 동일**하게 작동하는 것이 확인됨.
- 클론 제품에 따라 일부 GPIO(특히 P1.x 계열)가 비정상 동작하거나 내부 용도로 점유된 경우가 있을 수 있다.
- 분리형 키보드의 경우 좌우 MCU가 동일 모델이어도 overlay에서 row-gpios를 **별도로 재정의**해야 한다 (좌우 납땜 방향이 다를 수 있음).

## 관련 문서

- [pro_micro 핀 번호 체계](/zmk-pro-micro-pin-mapping.md)
- [어댑터 배선 및 거버 분석](/adapter-holder-wiring.md)
