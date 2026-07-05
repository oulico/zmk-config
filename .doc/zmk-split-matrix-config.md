---
type: Reference
title: ZMK 분리형 키보드 매트릭스 설정
description: kscan-gpio-matrix, matrix-transform, split overlay(col-offset, row/col 재정의) 설정 방법과 디버깅 패턴
tags: [zmk, kscan, matrix-transform, split-keyboard, overlay, col-offset, col2row]
timestamp: 2026-07-05
---

## 파일 구조

```
boards/shields/skeletyl/
├── skeletyl.dtsi          # 공통 정의 (transform, kscan base)
├── skeletyl_left.overlay  # 좌측 col-gpios 추가
├── skeletyl_right.overlay # 우측 row-gpios 재정의 + col-gpios + col-offset
├── skeletyl-layouts.dtsi  # 물리 레이아웃 (키 x/y 좌표)
└── skeletyl.keymap        # 레이어/바인딩 정의
```

## kscan 설정 (col2row 방식)

```c
default_kscan: kscan_0 {
    compatible = "zmk,kscan-gpio-matrix";
    diode-direction = "col2row";
    row-gpios = < ... (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN) >;
    // col-gpios는 각 overlay에서 추가
};
```

- `col2row`: 컬럼을 HIGH로 구동 → 행에서 감지
- `row-gpios`에 `GPIO_PULL_DOWN` 필수 (col2row에서 행은 풀다운 입력)
- `col-gpios`에는 플래그 없이 `GPIO_ACTIVE_HIGH`만

## Transform 설정

```c
map = <
RC(0,0) RC(0,1) RC(0,2) RC(0,3) RC(0,4)   RC(0,9) RC(0,8) RC(0,7) RC(0,6) RC(0,5)
...
>;
```

- `RC(row, col)` — 절대 행/열 번호 (col-offset 포함 전)
- 맵의 **N번째 항목** = 키맵 바인딩 **position N**
- 우측 col을 내림차순(9→5)으로 쓰는 이유: 물리 레이아웃 파일의 x좌표가 내측(x=900)→외측(x=1300) 순이기 때문

## Right Overlay — col-offset과 row 재정의

```c
// skeletyl_right.overlay
&default_transform {
    col-offset = <5>;   // 우측 col은 절대 번호 5부터 시작
};

&default_kscan {
    row-gpios           // 좌측과 순서가 다를 수 있으므로 반드시 재정의
        = <&pro_micro  4 (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)>
        , ...
        ;
    col-gpios           // 우측 col 핀 목록
        = <&pro_micro 19 GPIO_ACTIVE_HIGH>
        , ...
        ;
};
```

### col-offset 동작 원리

`col-offset = <5>`이면 우측의 local col0 → absolute col5, local col4 → absolute col9.  
Transform의 `RC(r, 5..9)`가 우측 `col-gpios[0..4]`에 대응된다.

## 물리 레이아웃 ↔ Transform ↔ 키맵 관계

```
물리 키 위치(x,y)
    ↓ skeletyl-layouts.dtsi (physical layout)
키맵 position 번호 (0-35)
    ↑ default_transform map의 N번째 항목 = position N
RC(row, col)
    ↑ kscan이 (row, col) 활성화를 감지
pro_micro 핀 조합 (row-gpios × col-gpios)
```

**x좌표로 position 파악:**
- x=0: Q(pos0), x=100: W(pos1) … 좌측 외→내
- x=900: Y(pos5), x=1000: U(pos6) … 우측 내→외 (position은 Y부터)
- x=1300: P(pos9)

## 우측 col 역순이 필요한 이유

물리 레이아웃에서 우측 position은 **5(Y, 내측) → 9(P, 외측)** 순이다.  
그러나 어댑터 배선은 P(col1_r, pro_micro 19) → Y(col5_r, pro_micro 7) 순(외→내)이다.

따라서 transform에서:
- `position 5 (Y)` = `RC(0,9)` → `col-gpios[4]` = pro_micro 7 (Y 물리키)
- `position 9 (P)` = `RC(0,5)` → `col-gpios[0]` = pro_micro 19 (P 물리키)

col-gpios 순서가 외→내인데, transform은 내→외(Y→P) 순이므로 **transform에서 col 번호를 역순(9→5)**으로 나열해야 한다.

## 디버깅 패턴

| 증상 | 가능한 원인 | 확인 방법 |
|------|------------|----------|
| 열 전체 미작동 | col-gpios 핀 번호 틀림 또는 단선 | 통전 테스트 (키 → MCU pad 전 구간) |
| 키 출력이 다른 키로 나옴 | transform 순서 오류 | 어느 키 눌렀을 때 어떤 출력 나오는지 매핑 |
| 좌우 중 한쪽만 틀림 | overlay에서 row-gpios 재정의 누락 | right overlay에 row-gpios 명시적으로 재정의 |
| 홈로우만 안 됨 | row 순서 오류 | 작동하는 행과 안 되는 행의 row-gpios 핀 교차 확인 |
| 엄지 일부 미작동 | transform의 thumb 행이 다른 RC row 가리킴 | 물리 납땜 방식 확인 후 transform 수동 조정 |

## 관련 문서

- [pro_micro 핀 번호 체계](/zmk-pro-micro-pin-mapping.md)
- [어댑터 배선 및 거버 분석](/adapter-holder-wiring.md)
- [nRF52840 / Nice!Nano 핀 배치](/nrf52840-nicenano-pinout.md)
