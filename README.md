# 🚀 Android Engineering Playbook & Architecture Decisions

![Kotlin](https://img.shields.io/badge/Kotlin-2.2.10-blue?logo=kotlin)
![AGP](https://img.shields.io/badge/AGP-9.1.0-3DDC84?logo=android)
![Compose](https://img.shields.io/badge/Jetpack_Compose-Runtime-4285F4?logo=jetpackcompose)

반갑습니다. **Android Product & Platform Architect** 하윤형입니다. 

대규모 트래픽을 감당하는 **C2C/B2C 커머스 환경의 복잡한 비즈니스 로직 설계**부터, 전사 개발자들의 생산성을 지휘하는 **코어 인프라(빌드 파이프라인, 거대 디자인 시스템) 구축**까지 앱의 '표면(Product)'과 '심연(Platform)'을 모두 설계합니다.

본 레포지토리는 제가 다양한 도메인의 엔터프라이즈 모바일 환경에서 **"비즈니스 요구사항을 달성하면서 동시에 기술 부채를 어떻게 해결했는지"**를 기록한 아키텍처 의사결정(ADR) 도서관입니다. 단순한 화면 구현을 넘어, 성능 최적화, 자동화, 그리고 시스템 확장성을 결정지었던 치열한 고민과 **트레이드오프(Trade-offs)**에 대해 서술합니다.

---

## 🏗 인프라스트럭처 및 빌드 자동화 (Build Logic & Infra)

멀티 모듈 구조에서의 유지보수 지옥을 피하기 위한 중앙화 설정 및 마이그레이션 기록입니다. 쓸모없는 보일러플레이트를 가장 혐오합니다.

* [ADR-001: 5계층 멀티 모듈 캡슐화를 위한 AGP 9.1 및 K2 컴파일러 마이그레이션](./decisions/ADR-001-agp9-convention-plugins.md)
* [ADR-003: CI/CD 배포 리드타임 90% 단축을 위한 Firebase 자동 배포 파이프라인](./decisions/ADR-003-cicd-firebase.md) *(예시)*

## 🧪 시각적 품질 통제 (Visual Testing & QA Automation)

앱의 규모가 커도, 아무도 테스트 코드를 손으로 짜지 않게 만드는 100% 자동화 인프라를 지향합니다.

* [ADR-002: 휴먼 에러 차단을 위한 Showkase(KSP) + Paparazzi 완전 자동화 렌더링망 구축](./decisions/ADR-002-auto-paparazzi-testing.md) (🔥 개발자 테스트 개입 도달률 0% 달성)
* [ADR-005: Custom Ktlint Rules를 통한 사내 디자인 토큰 강제화 엔진](./decisions/ADR-005-custom-lint-rules.md) *(예시)*

## 🛠 분리된 오픈소스 도구 (Standalone Tools)

사내 프로젝트에서 발생한 반복적인 고통(Pain-points)을 해결하기 위해 제가 직접 개발하여 외부에 추출(Open Source)한 독립 도구 모음입니다.

* 📦 **[modern-agp9-convention-template](https://github.com/glossybigbro/modern-agp9...)**: 스마트 캐스팅을 적용한 AGP 9.1 보일러플레이트 템플릿
* 📦 **[compose-fluid-typography](https://github.com/glossybigbro/compose-fluid...)**: 디바이스 크기나 접근성 설정에 따라 물결처럼 폰트가 변환되는 커스텀 Modifier 라이브러리 *(예시)*

---

### 📝 My Design Philosophy (아키텍처 철학)
1. **SSOT (Single Source of Truth):** 빌드, 디자인 토큰, 아키텍처는 무조건 단일 진실 공급원을 가져야 합니다.
2. **DX is UX:** 동료 개발자들의 생산성을 올리는 도구가, 결과적으로 세계 최고의 앱 사용 경험을 만듭니다.
3. **Data-Driven Impact:** 수치로 증명할 수 없다면 아키텍처 개선이 아닙니다. 코드를 지우는 행위를 가장 사랑합니다.

> 🌐 **Full Portfolio:** [glossybigbro.github.io](https://glossybigbro.github.io)
