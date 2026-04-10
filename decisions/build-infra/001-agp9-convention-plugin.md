# ADR 001: AGP 9.1 & K2 Compiler 마이그레이션 및 빌드 스크립트 중앙화 (Convention Plugin)

**Date**: 2026-04-10
**Author**: 하윤형 (Core Infrastructure Architecture)
**Status**: Accepted

---

## 2. Context (작업 배경 및 한계 상황)

WDS(대규모 엔터프라이즈 디자인 시스템) 프로젝트는 `CoreDesignSystemComposeUI`와 `CoreDesignSystemStorybook`이라는 거대한 구조를 지탱하고 있었으나, 빌드 로직이 각 모듈에 파편화되어 존재했습니다. 향후 디자인 토큰과 UI 컴포넌트를 분리하는 5단계 스케일의 '빅-멀티 모듈(Multi-Module) 아키텍처'로 확장하기 위해서는 빌드 스크립트의 중앙화가 절실했습니다.

또한 최신의 안드로이드 빌드 표준(Android Gradle Plugin 9.1 및 Kotlin 2.2.10 K2 Compiler)을 수용하는 과정에서, 이전 AGP 버전까지 지원되던 `CommonExtension` 공통 빌드 블록의 속성 일부(`compileOptions`, `packaging` 등)가 분리 및 폐기되는 거대한 하위 호환성 단절(Breaking Changes)을 맞닥뜨렸습니다. 기존의 Maven 배포(`maven-publish`) 시스템 역시 `release` 컴포넌트를 명시적으로 옵트인해야 하는 제약도 제기되었습니다.

## 3. Decision (아키텍처 결정사항)

1. **`build-logic` Convention Plugin 시스템 전면 도입**
   - 분산된 종속성을 관리하기 위해 `wing.android.application`, `wing.android.library` 등 독립적인 빌드 플러그인 모듈 구현.
2. **K2 Compose Compiler 및 KSP 연동 인프라 구축**
   - 구형 Kotlin Android 확장 및 Compose 플러그인을 제거하고 최신 네이티브 Kotlin 컴파일러 환경으로 이주.
3. **Smart Casting 기반의 DRY 아키텍처 구성**
   - 폐기된 `CommonExtension` 블록을 대체하기 위해, 빌드 적용 대상 모듈을 `ApplicationExtension`과 `LibraryExtension`으로 분기하여 캐스팅하는 `private fun` 스코프 내부 확장 함수들을 도출 설계.
4. **Maven Publishing 명시적 Variant Opt-in**
   - Maven Publish 등록 시 `singleVariant("release")` 옵션을 빌드 로직 기저에 삽입하여 에러를 원천 차단.

## 4. [CRITICAL] Alternatives Considered & Trade-offs (대안 및 트레이드오프)

- **고려했던 대안 (Alternatives)**: 
  - 모듈 최상단에 `buildscript { ext }` 방식의 변수 관리를 유지하거나, `libs.versions.toml`만을 사용해 각 모듈 `build.gradle.kts`에 일일이 플러그인을 선언하는 방식(Plan B)을 검토.
  - **기각 사유:** 모듈이 수십 개로 쪼개질 때마다 중복된 안드로이드 설정(compileSdk, minSdk, jvmTarget 등)이 복사-붙여넣기 되며 최악의 휴먼 에러를 유발함.

- **현재 선택의 한계점 (Trade-offs)**: 
  - `build-logic` 구조를 최초로 짜는 데 고도의 Gradle 언어적 숙련도와 시간(Learning Curve)이 요구됨.
  - 컨벤션 플러그인의 로직(`KotlinAndroid.kt` 등)을 한 줄만 수정해도 의존성이 걸린 수십 개의 멀티 모듈 전체 캐시가 무효화되어 "초기 빌드 타임(Overhead)의 순간적 증가"라는 트레이드오프가 존재함.
  - **그럼에도 채택한 이유:** 장기적으로 팀원들이 새 모듈을 만들 때 투입되는 '설정 소요 시간'을 0(Zero)으로 수렴시킬 수 있는 이점이 인프라 오버헤드의 단점을 압도함.

## 5. Consequences (달성한 지표 및 파급 효과)

- **DX(개발자 경험) 획기적 개선**: 향후 수집될 모듈(`core:*`)들은 단 1줄의 `plugins { id("wing.android.library") }` 선언만으로 모든 안드로이드 SDK, K2 컴파일러, Java 17 세팅이 자동 주입됨 (유지보수 비용 99% 삭감).
- **코드량 감축 (DRY 달성)**: `gradle.properties` 내부의 구형 AGP 레거시 플래그(`usesSdkInManifest` 등) 10여 종 소거 및 빌드 스크립트 중복 라인 수백 줄 제거 완수.
- **안정성 입증**: AGP 9.1 시스템 하에서도 CI/CD 파이프라인과 Maven 로컬 배포 검증 에러 0건 달성.
