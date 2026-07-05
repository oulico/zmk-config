# Oulico Skeletyl — ZMK 펌웨어 지식 문서

Oulico Skeletyl 커스텀 분리형 키보드 제작 과정에서 습득한 기술 지식을 OKF(Open Knowledge Format) v0.1 형식으로 정리한 문서 모음.

## 문서 목록

- [pro_micro 핀 번호 체계](./zmk-pro-micro-pin-mapping.md) — AVR D-pin → ZMK pro_micro 인덱스 변환 및 실제 사용 핀 목록
- [nRF52840 / Nice!Nano 핀 배치](./nrf52840-nicenano-pinout.md) — nRF52840 GPIO와 Pro Micro 풋프린트 매핑
- [어댑터(홀더) 배선 및 거버 분석](./adapter-holder-wiring.md) — J11/J12 커넥터 핀아웃, net→pro_micro 대응표
- [ZMK 분리형 키보드 매트릭스 설정](./zmk-split-matrix-config.md) — kscan, transform, split overlay 설정 방법 및 디버깅 패턴
- [Nice!Nano 부트로더 진입법](./nicenano-bootloader.md) — UF2 모드 진입, LED 상태 해석, 납땜 불량 시 RST 패드 쇼트 방법
- [ZMK HRM 튜닝](./zmk-hrm-tuning.md) — tapping-term/require-prior-idle-ms 의미, Shift 분리 패턴, 한영전환 간섭 해결
