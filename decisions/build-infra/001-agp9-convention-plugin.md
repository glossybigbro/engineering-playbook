# ADR 001: AGP 9.1 & K2 Compiler 마이그레이션 및 빌드 스크립트 중앙화 (Convention Plugin)

**Date**: 2026-04-10
**Author**: 하윤형 (Android Product & Platform Architect)
**Status**: Accepted

---

## 1. Context (작업 배경: 레거시 환경의 기술 부채)

제가 프로젝트에 합류하여 빌드 인프라를 진단했을 당시, 시스템의 외형은 엔터프라이즈 디자인 시스템의 규모를 갖추었으나 실질적인 빌드 파이프라인은 단일(Monolithic) 구조의 파편화된 잔재에서 벗어나지 못한 상태였습니다.

- **심각한 보일러플레이트 (Spaghetti Config):** 각 모듈마다 `build.gradle.kts` 내부에 100줄 이상의 동일한 안드로이드 설정이 하드코딩되어 있었습니다. 모듈을 추가할 때마다 발생하는 'Copy & Paste' 안티패턴으로 인해 버전 충돌(Version Conflict)과 빌드 파손이 빈번했습니다.
- **모듈 확장성 결여 (Bottleneck):** 향후 디자인 토큰과 UI 컴포넌트를 분단하는 5개 계층의 '빅-멀티 모듈(Multi-Module)'로 확장하기 위해서는, 피처(Feature) 개발자가 빌드 설정에 전혀 신경 쓰지 않게 만드는 **제로 컨피규레이션(Zero-Configuration) 인프라**가 필수적이었습니다.

**[기술적 허들: AGP 9.1 Breaking Changes]**
이러한 레거시를 뿌리 뽑음과 동시에, 최신의 안드로이드 빌드 표준(AGP 9.1 및 K2 Compiler)을 수용해야 했습니다. 이전 단계까지 쓰이던 `CommonExtension` 공통 빌드 블록의 핵심 속성(`compileOptions`, `packaging` 등)이 모조리 폐기되거나 분리되는 거대한 하위 호환성 단절(Breaking Changes)을 맞닥뜨렸습니다. 결과적으로 단순한 코드 정리가 아닌, 빌드 환경 전체의 통제권을 중앙화(Centralize)하는 전면 재설계가 불가피했습니다.

## 2. Decision (아키텍처 결정사항)

1. **`build-logic` Convention Plugin 시스템 전면 도입**
   - 분산된 종속성과 빌드 로직을 단일 진실 공급원(SSOT)으로 묶기 위해 `wing.android.application`, `wing.android.library` 등 독립된 플러그인 계층을 구축했습니다.
2. **K2 Compose Compiler 및 KSP 연동 인프라 구축**
   - 구형 Kotlin Android 확장 플러그인을 제거하고, 최신 네이티브 Kotlin 컴파일러 및 최적화된 KSP 환경으로 이주했습니다.
3. **Smart Casting 기반의 DRY 아키텍처 설계**
   - 폐기된 `CommonExtension` 블록을 안전하게 대체하기 위해, 적용 대상 모듈을 `ApplicationExtension`과 `LibraryExtension`으로 명시적 스마트 캐스팅(Smart Cast)하여 처리하는 `private extension` 블록을 설계했습니다.

## 3. [CRITICAL] Alternatives Considered & Trade-offs

- **고려했던 대안 (Alternatives)**: 
  - 루트 경로에 `buildscript { ext }` 방식의 변수를 유지하거나, `libs.versions.toml`만을 사용해 각 모듈에 일일이 플러그인을 수동 선언하는 방식을 검토함.
  - **기각 사유:** 모듈이 수십 개로 쪼개질 경우, SDK 버전이나 컴파일 타겟이 파편화되어 최악의 휴먼 에러 레이더(Radar)에 노출됨.

- **선택의 한계점 (Trade-offs)**: 
  - `build-logic` 내부의 코드(예: `KotlinAndroid.kt`) 한 줄만 수정해도, 이에 의존하는 수십 개의 워크스페이스 모듈 전체의 캐시가 무효화되어 '초기 빌드 타겟팅 오버헤드'를 유발할 수 있음.
  - **극복 사유:** 장기적으로 봤을 때, 팀원들이 새 모듈을 만들거나 컴포넌트를 분리할 때 소비하는 **'인프라 설정 시간'을 0(Zero)으로 수렴시킬 수 있는 개발자 경험(DX)의 압도적 이점**이 초기 빌드 속도의 단점을 상쇄함.

## 4. Consequences (달성 지표 및 파급 효과)

- **90% 이상의 보일러플레이트 제거 (DRY 달성)**: 기존 모듈의 100줄짜리 설정 코드를 단 1줄의 `plugins { id("wing.android.library") }` 선언만으로 대체하여 코드 베이스의 유지보수 비용을 극단적으로 낮추었습니다.
- **Onboarding(온보딩) 리드타임 제로화**: 동료 개발자들은 안드로이드 SDK 버전, Java 17 세팅 등을 파악할 여력 없이 "오직 화면과 비즈니스 로직(Product)"에만 집중할 수 있는 완벽한 샌드박스 환경을 획득했습니다.
- **안정성 입증**: AGP 레거시 플래그(`usesSdkInManifest` 등) 10여 종을 소거하고 Maven 배포 시스템의 명시적 옵트인(`singleVariant`) 제약을 해결하여, CI/CD 환경에서의 빌드 에러율 0%를 달성했습니다.
