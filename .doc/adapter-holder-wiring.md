---
type: Reference
title: 어댑터(홀더) 배선 및 거버 분석
description: Oulico Skeletyl 어댑터 PCB의 커넥터 핀아웃, net 이름과 pro_micro 번호 대응표, 거버 파일 분석 방법
tags: [adapter, holder, gerber, j11, j12, connector, wiring, kicad]
timestamp: 2026-07-05
resource: ~/Downloads/gerber/adapter-F_Cu.gbr
---

## 어댑터 PCB 구성

어댑터 보드는 Nice!Nano(또는 클론) MCU와 분리형 키보드 본체 사이를 연결한다.

- **U1** — 좌측 MCU 소켓
- **U2** — 우측 MCU 소켓
- **J9, J10** — 좌측 키보드 커넥터 (5핀, 6핀)
- **J11, J12** — 우측 키보드 커넥터 (5핀, 6핀)

## 커넥터 핀아웃

### J11 (우측, 5핀) — 주요 컬럼

| 핀 번호 | Net | pro_micro | 용도 |
|---------|-----|-----------|------|
| 1 | col3_r | 10 | I열 |
| 2 | row1_r | 5 | row1 |
| 3 | col2_r | 20 | O열 |
| 4 | col1_r | 19 | P열 (새끼) |
| 5 | row2_r | 18 | row2 |

### J12 (우측, 6핀) — 내측 컬럼 + row

| 핀 번호 | Net | pro_micro | 용도 |
|---------|-----|-----------|------|
| 1 | row4_r | 4 | row0 |
| 2 | row3_r | 21 | row3 |
| 3 | col4_r | 6 | U열 |
| 4 | col5_r | 7 | Y열 (검지 내측) |
| 5 | col6_r | 8 | (예비) |
| 6 | row5_r | 9 | row4 예비 |

### J9 (좌측, 5핀) / J10 (좌측, 6핀)

좌측은 우측과 대칭 구조. net 이름만 `_l`로 바뀜 (col1_l, col2_l … row4_l 등).

## U2 pad → net → pro_micro 대응표 (주요 핀)

| U2 pad | AVR 표기 | Arduino D-pin | net | pro_micro |
|--------|----------|--------------|-----|-----------|
| 9 | PD7 | D6* | col4_r | 6 |
| 10 | PE6 | D7 | col5_r | 7 |
| 11 | PB4 | D8 | col6_r | 8 |
| 12 | PB5 | D9 | row5_r | 9 |
| 13 | PB6 | D10 | col3_r | 10 |
| 18 | PF6 | A1/D19 | col1_r | 19 |
| 19 | PF5 | A2/D20 | col2_r | 20 |
| 20 | PF4 | A3/D21 | row1_r(우) | 21 |
| 21 | PF1 | — | row2_r | 18 |

> ⚠️ **D20/D21 역전 주의:** 거버에서 pad 20이 `PF4=F4`로 표기되는데, AVR에서 PF4는 A3 = Arduino **D21** 이다. pad 번호(20)와 Arduino D-pin(21)이 달라서 혼동하기 쉽다.

## 거버 파일 분석 방법

1. KiCad Gerber Viewer 또는 Gerbv로 `adapter-F_Cu.gbr` 열기
2. **Net Inspector**에서 col/row net 이름으로 해당 pad 위치 확인
3. 실크스크린(`adapter-F_Silkscreen.gbr`)은 벡터 그래픽 포맷이라 grep 불가 — KiCad에서 시각적으로 확인해야 함
4. `adapter-PTH.drl` 드릴 파일로 through-hole pad 위치 파악 가능

## 납땜 시 유의사항

- **양쪽 키보드는 미러 대칭이 아닐 수 있다.** 납땜 방향에 따라 좌측과 우측의 col-gpios가 달라야 한다.
- 어댑터 홀더의 라벨(예: "C1") 위치가 어느 net에 해당하는지 거버에서 사전 확인 필수.
- 통전 테스트는 키 스위치 → 커넥터 패드 구간만 확인한다. **커넥터 패드 → MCU 패드 구간도 별도 확인** 필요.

## 관련 문서

- [pro_micro 핀 번호 체계](/zmk-pro-micro-pin-mapping.md)
- [ZMK 분리형 키보드 매트릭스 설정](/zmk-split-matrix-config.md)
