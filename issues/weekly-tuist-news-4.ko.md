# Weekly Tuist News #4

> Draft — 사용자 검토 대기 중

---

이번 호는 뉴스거리가 없어서 미뤘다가 개인적으로 바빠지는 바람에 늦어졌습니다. 그 사이 CLI 정식 릴리즈가 두 번(4.206.0, 4.207.0) 나왔고, 안정성 쪽 업데이트가 많았습니다.

**1. CLI 4.207.0 — 캐시 연결 방식이 새 기본값으로 전환**

`tuist setup cache`를 실행하면 이제 프로젝트별 캐시 데몬 대신 **머신 전역 CAS(Content-Addressable Storage) 프록시 + 플러그인**이 기본으로 설치됩니다. 새로 generate한 프로젝트도 기존 `COMPILATION_CACHE_REMOTE_SERVICE_PATH` 빌드 설정 대신 이 CAS 플러그인 설정을 기본으로 받습니다.

문제가 생기면 `TUIST_FEATURE_FLAG_KURA=0` 환경변수로 예전 방식(레거시 데몬 + 예전 빌드 설정)으로 되돌릴 수 있습니다. CI에서는 새 방식을 쓰고 로컬 개발 머신은 예전 방식을 유지하는 식으로 환경별로 따로 설정도 가능하다네요.

그 외에도 `.xcresult`로 안 끝나는 이름의 xcresult 번들을 인식하도록 완화됐고, `xctestrun` 기반 selective testing 지원 버그도 고쳐졌다고 합니다.

원문: [4.207.0 릴리즈 노트](https://github.com/tuist/tuist/releases/tag/4.207.0)

**2. CLI 4.206.0 — Air 무료 티어 캐시 사용량 제한 적용**

무료 요금제(Air)의 사용량 제한이 live cache lane 전반에 적용되기 시작했습니다. 지금까지 캐시를 사실상 무제한으로 쓰고 있었다면 이번 업데이트로 제한을 체감할 수 있습니다. 같이 들어간 기능으로, `tuist generate` 실행 시 현재 적용 중인 binary cache 설정이 로그에 노출됩니다. 버그 수정은 visionOS UI test target 의존이 막혀있던 문제, release 빌드마다 DerivedData 경로가 바뀌어 컴파일 캐시가 안 먹던 문제, Objective-C 리소스 접근자가 Swift 전용으로만 생성되던 문제 등이 고쳐졌다네요.

원문: [4.206.0 릴리즈 노트](https://github.com/tuist/tuist/releases/tag/4.206.0)

**3. 빌드 시스템의 원리**

Tuist 공식 블로그에 올라온 빌드 그래프, incremental build, 원격 캐싱, 원격 실행 이 네 가지가 왜 체감 빌드 시간을 개선하는지 정리한 내용입니다. 원격 캐시나 원격 실행을 아직 안 쓰더라도 빌드 시간을 고민 중이면 읽어볼 만합니다.

원문: [The physics of build systems](https://tuist.dev/blog/2026/08/25/the-physics-of-build-systems)

### Tuist Talk

[Slack](https://tuist-community.slack.com/archives/CCYNEGY1L/p1788506463708969)에서는 한 사용자가 자신의 사이드 프로젝트를 오픈소스로 공개하면서 Tuist Dashboard도 함께 공개해도 되는지 물었습니다.
Pedro는 감탄하며, 프로젝트마다 로고를 등록해두면 소셜에 링크를 공유할 때 Open Graph 이미지에 그 로고가 반영되게 하는 기능을 Marek Fořt와 논의 중이라고 답했습니다.

### Practice
원래 새로 추가된 Stress Test를 시험해보려고 했는데 아직 4.208가 릴리즈 되지 않아서 보류하기로 했습니다.
