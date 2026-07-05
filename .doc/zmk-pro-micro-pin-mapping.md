---
type: Reference
title: ZMK pro_micro 핀 번호 체계
description: AVR Pro Micro의 D-pin 표기가 ZMK에서 pro_micro 인덱스로 어떻게 대응되는지, 그리고 Oulico Skeletyl에서 실제 사용된 핀 목록
tags: [zmk, pro_micro, pin-mapping, nrf52840, kscan]
timestamp: 2026-07-05
---

## 핵심 개념

ZMK에서 `<&pro_micro N ...>` 표기의 N은 **Arduino/AVR Pro Micro의 D-pin 번호**와 동일하다.

| ZMK 표기 | AVR 포트/핀 | Arduino 이름 |
|----------|------------|-------------|
| `pro_micro 0` | PD2 | D0 (RX) |
| `pro_micro 1` | PD3 | D1 (TX) |
| `pro_micro 4` | PD4 | D4 |
| `pro_micro 5` | PC6 | D5 |
| `pro_micro 6` | PD7 | D6 |
| `pro_micro 7` | PE6 | D7 |
| `pro_micro 8` | PB4 | D8 |
| `pro_micro 9` | PB5 | D9 |
| `pro_micro 10` | PB6 | D10 |
| `pro_micro 14` | PB3 | MISO (D14) |
| `pro_micro 15` | PB1 | SCK (D15) |
| `pro_micro 16` | PB2 | MOSI (D16) |
| `pro_micro 18` | PF7 | A0 (D18) |
| `pro_micro 19` | PF6 | A1 (D19) |
| `pro_micro 20` | PF5 | A2 (D20) |
| `pro_micro 21` | PF4 | A3 (D21) |

> **주의:** 어댑터 PCB 거버에서 pad를 "F4=PF4" 형태로 표기하면, 이는 AVR 포트 F, 비트 4 = D21 = `pro_micro 21`을 의미한다.

## Oulico Skeletyl 실제 핀 배정

### 좌측 (Left overlay)

| pro_micro | 용도 |
|-----------|------|
| 21 | row0 (상단행) |
| 18 | row1 |
| 5 | row2 |
| 4 | row3 (하단행) |
| 9 | row4 (엄지, 예비) |
| 19 | col0 (Q열, 새끼손가락) |
| 20 | col1 (W열) |
| 10 | col2 (E열) |
| 6 | col3 (R열) |
| 7 | col4 (T열, 검지 내측) |

### 우측 (Right overlay)

| pro_micro | 용도 |
|-----------|------|
| 4 | row0 |
| 5 | row1 |
| 18 | row2 |
| 21 | row3 |
| 9 | row4 (예비) |
| 19 | col0→absolute col5 (P열, 새끼) |
| 20 | col1→absolute col6 (O열) |
| 10 | col2→absolute col7 (I열) |
| 6 | col3→absolute col8 (U열) |
| 7 | col4→absolute col9 (Y열, 검지 내측) |

## 교훈

- 어댑터 거버의 AVR 포트 표기(예: F4, F5, F6)를 보고 `pro_micro` 번호를 잘못 읽는 실수가 자주 발생한다.
- D20(PF5)와 D21(PF4)처럼 AVR 비트 순서와 Arduino D-pin 순서가 역전되는 경우가 있으므로 반드시 변환표로 확인한다.
- **통전 테스트가 통과해도 펌웨어 핀 번호가 틀리면 작동하지 않는다.** 반대로, 통전 테스트 실패는 하드웨어 문제다.
