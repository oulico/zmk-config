---
name: zmk-hrm-tuning
description: ZMK 홈로우 모디파이어(HRM) 타이밍 파라미터 의미, 한영전환 콤보 간섭 문제, Shift 분리 패턴
tags: [zmk, hrm, hold-tap, require-prior-idle-ms, tapping-term, language-switch]
timestamp: 2026-07-05
---

## 핵심 파라미터

### tapping-term-ms
홀드로 인정하는 최소 누름 시간. 이 시간 이상 누르면 → hold(모디파이어). 미만이면 → tap(문자).
- 기본값: 200ms
- 낮출수록 모디파이어 발동이 빨라지지만 오작동 위험 증가

### quick-tap-ms
같은 키를 빠르게 두 번 누를 때(더블탭 후 홀드) tap-repeat로 처리하는 임계값. 키 반복에 유용.
- 권장: tapping-term의 약 75~85%

### require-prior-idle-ms
**핵심 파라미터.** 직전 키 입력으로부터 이 시간이 지나야만 hold가 인정된다.
- 150ms 이내에 다른 키를 쳤으면 → 무조건 즉시 tap 처리
- 효과: 빠른 타이핑 롤링 중 홈로우가 절대 모디파이어로 오작동하지 않음
- **부작용: 150ms 안에 눌린 엄지 키나 콤보도 차단됨**

## 권장 설정 (빠른 타이피스트)

```c
hml: homerow_mods_left {
    flavor = "balanced";
    tapping-term-ms = <200>;
    quick-tap-ms = <175>;
    require-prior-idle-ms = <150>;
    hold-trigger-key-positions = <KEYS_R THUMBS>;
    hold-trigger-on-release;
};
```

## Shift 분리 패턴

Shift 오작동은 대문자 하나로 끝나지만, Cmd/Ctrl 오작동은 위험한 단축키를 유발한다.
따라서 Shift 전용 behavior를 분리해 `require-prior-idle-ms`를 제거한다.

```c
hml_shift: homerow_mods_left_shift {
    flavor = "balanced";
    tapping-term-ms = <200>;
    quick-tap-ms = <175>;
    // require-prior-idle-ms 없음 → 롤링 즉시 Shift 발동
    hold-trigger-key-positions = <KEYS_R THUMBS>;
    hold-trigger-on-release;
};
```

F 키 → `&hml_shift LSHFT F`, J 키 → `&hmr_shift LSHFT J`

## 한영전환과 hold-tap 간섭 문제

### 문제
콤보(예: ESC+DEL 동시)로 한영전환 구현 시, 콤보 키 중 하나가 `tlt`(hold-tap)이면:
- `require-prior-idle-ms`가 콤보를 차단하거나
- hold-tap이 먼저 발동해 레이어가 열리면서 콤보 키가 다른 key로 바뀜

### 해결책: hold-then-tap 방식
콤보 대신 "DEL 홀드 → FUN 레이어 → ESC 탭 = F18" 구조를 사용한다.
- DEL에 `require-prior-idle-ms` 없는 별도 hold-tap 적용
- tapping-term을 120ms로 낮춰 빠른 발동

```c
lt_del: layer_tap_del {
    flavor = "balanced";
    tapping-term-ms = <120>;   // 빠른 발동
    quick-tap-ms = <175>;
    // require-prior-idle-ms 없음
    bindings = <&mo>, <&kp>;
};
```

FUN 레이어 ESC 위치(LH0) = `&kp F18` (한영전환)

### 왜 콤보보다 hold-then-tap이 안정적인가
- 콤보: 두 키를 timeout(ms) 안에 동시에 눌러야 함 → 타이밍에 민감
- hold-then-tap: 한 손으로 홀드, 다른 손으로 탭 → 자연스럽고 실패 없음

## 관련 문서

- [ZMK 분리형 키보드 매트릭스 설정](./zmk-split-matrix-config.md)
