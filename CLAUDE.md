# Oulico Skeletyl — ZMK Config

## 프로젝트 개요

nRF52840 기반 Nice!Nano 호환 클론을 사용한 커스텀 분리형 키보드 ZMK 펌웨어 설정.  
GitHub Actions CI로 빌드하며, `fix/miryoku` 브랜치에서 작업 중.

## 기술 문서

작업 과정에서 습득한 핵심 지식은 `.doc/` 폴더에 OKF(Open Knowledge Format) 형식으로 정리되어 있다.

| 문서 | 내용 |
|------|------|
| [`.doc/index.md`](./.doc/index.md) | 문서 목록 |
| [`.doc/zmk-pro-micro-pin-mapping.md`](./.doc/zmk-pro-micro-pin-mapping.md) | AVR D-pin → ZMK pro_micro 번호 변환표, 실제 핀 배정 |
| [`.doc/nrf52840-nicenano-pinout.md`](./.doc/nrf52840-nicenano-pinout.md) | nRF52840 GPIO ↔ Pro Micro 풋프린트 매핑 |
| [`.doc/adapter-holder-wiring.md`](./.doc/adapter-holder-wiring.md) | 어댑터 PCB J11/J12 커넥터 핀아웃, 거버 분석 방법 |
| [`.doc/zmk-split-matrix-config.md`](./.doc/zmk-split-matrix-config.md) | kscan, transform, split overlay 설정 원리 및 디버깅 |

## 하드웨어 요약

- MCU: Nice!Nano v2 호환 AliExpress 클론 (좌우 동일 모델)
- 어댑터: 커스텀 홀더 PCB (U1=좌측, U2=우측, J9/J10=좌측 커넥터, J11/J12=우측 커넥터)
- 거버 파일: `~/Downloads/gerber/adapter-F_Cu.gbr` 외
