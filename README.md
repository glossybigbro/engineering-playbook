# Android Architecture & Platform Playbook

![Kotlin](https://img.shields.io/badge/Kotlin-2.2.10-black?style=flat-square&logo=kotlin) ![AGP](https://img.shields.io/badge/AGP-9.1.0-black?style=flat-square&logo=android) ![Jetpack Compose](https://img.shields.io/badge/Compose-Compiler-black?style=flat-square&logo=jetpackcompose)

**Android Product & Platform Architect 하윤형입니다.**  
대규모 C2C/B2C 커머스의 비즈니스 로직 설계부터 전사 개발자를 위한 코어 인프라 구축까지, 프로덕트의 표면과 시스템의 심연을 모두 관장합니다.

현업에서 마주한 기술 부채를 해결하며 도출한 아키텍처 의사결정과 트레이드오프(Trade-offs)를 기록하는 도서관입니다. 단순한 클린 코드보다는, 수치로 증명할 수 있는 파이프라인 최적화와 시스템의 아키텍처 무결성에 집착합니다.

> "DX is UX. 자동화된 개발자 경험만이 최고의 사용자 경험을 보장한다."

---

## Architecture Decisions (ADR)

수십 명의 개발자가 의존하는 거대 시스템의 일관성을 통제하기 위해 구축한 아키텍처 결정 기록입니다.

| Domain | Document | TL;DR |
|:---|:---|:---|
| **Build & Infra** | [ADR-001: 애그리게잇 멀티 모듈 및 AGP 9.1 마이그레이션](./decisions/build-infra/001-agp9-convention-plugin.md) | 보일러플레이트를 제거하고 빌드 스크립트를 중앙화하는 플러그인 5계층 추상화 |
| **Visual Testing** | _(Drafting: Paparazzi 자동화 테스트망 문서)_ | 휴먼 에러 차단을 위한 Zero-touch 컴포넌트 시각적 회귀 파이프라인 |

---

## Standalone Extractions (Open-Source Tools)

사내 코어 시스템을 구축하는 과정에서 파생된 인프라적 고통(Pain-points)을 범용적으로 해결하기 위해 **외부로 추출 준비 중**인 독립 도구 모음입니다.

| Tool Name | Scope | Status | Description |
|:---|:---|:---:|:---|
| `modern-agp9-template` | Build Script | ⏳ _WIP_ | 스마트 캐스팅 기반의 무중단 멀티모듈 빌드 템플릿 (추출 준비중) |
| `zero-touch-paparazzi` | KSP / QA | ⏳ _WIP_ | 보일러플레이트 없는 Compose 단위 컴포넌트 자동 캡처 엔진 (개발중) |
