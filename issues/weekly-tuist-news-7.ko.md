# Weekly Tuist News #7

> Note: 한국어 초안 승인됨. 아직 Medium에 발행되지 않았습니다.

---

이번 호는 보안 패치 하나, 그리고 CLI 4.216.0과 4.217.0, 이렇게 세 가지를 다룹니다. 이번에는 CLI를 쓰고 있다면 버전부터 확인해 보시길 권합니다.

**1. TLS 인증서 검증 보안 업데이트: CLI 4.214.1 (10/30)**

eMudhra 인증서 키가 유출된 사건에 대응한 보안 업데이트입니다. 영향을 받는 키로 만든 인증서 체인을 거부하고, 함께 배포되는 인증서 저장소도 갱신합니다. 일반적인 인증서·호스트명 검증은 그대로 켜져 있고, 별도의 사설 인증 기관도 계속 지원됩니다.

원문: [changelog 2026.10.30](https://tuist.dev/changelog/2026.10.30-tls-certificate-security-updates), [4.214.1 릴리즈 노트](https://github.com/tuist/tuist/releases/tag/4.214.1)

**2. CLI 4.216.0 (11/9): 테스트와 커버리지 옵션 확장**

기능이 많은 릴리즈입니다.

- `tuist test`에서 타겟 태그 선택자를 지원합니다.
- `tuist test --coverage-output-path`로 실행마다 커버리지를 LCOV로 저장할 수 있습니다.
- 생성되는 test plan이 Coverage 타겟을 지원하고, Locale을 고정할 수 있습니다. 기존 호환성은 유지됩니다.
- 모듈 캐시가 시뮬레이터와 기기 destination을 지원합니다.
- Cargo 테스트의 샤딩과 quarantine, Cargo 캐시 확장(공유 REAPI, chunk 재사용)이 들어왔습니다.
- 데스크톱과 CLI preview를 추가했습니다.

수정으로는 소스에 코드가 없거나 한 파일을 공유하는 타겟의 캐시 바이너리 저장, 공유 인증 정보 갱신의 직렬화, 클라이언트 연동 전반의 인증서 신뢰 강화(1번 항목과 같은 맥락)가 포함됩니다.

원문: [4.216.0 릴리즈 노트](https://github.com/tuist/tuist/releases/tag/4.216.0)

**3. CLI 4.217.0 (11/16): 커버리지 기반 테스트 선택과 sandbox**

- Coverage 기반 테스트 선택(coverage-based test selection)이 들어왔습니다.
- `tuist test`에서 destination을 선택할 수 있습니다.
- `tuist build bazel`로 Bazel 호출을 읽고, `tuist bazel setup --remote-execution`으로 원격 실행을 찾습니다.
- sandbox API, MCP 도구, `tuist sandbox` 명령이 추가되었습니다.
- Cargo 캐시가 relocatable하게 바뀌었고, pull request 빌드에서 CLI preview를 게시합니다.

수정도 많습니다. 예를 들어 SwiftPM 헤더가 수천 개인 패키지 타겟을 캐시하고, C++ 패키지 타겟을 쓰는 클라이언트에 C++ 표준 라이브러리를 링크하며, 캐시된 매크로 소비자가 Xcode compilation caching에서도 relocatable하도록 고쳤습니다.

원문: [4.217.0 릴리즈 노트](https://github.com/tuist/tuist/releases/tag/4.217.0)

**Tuist Talk: 4.214.1로 올렸더니 설치가 깨져요**

공개 [Tuist Slack](https://tuist-community.slack.com/archives/C018QG7U7SN/p1793620002488369)에서 한 사용자가, 필수라고 안내된 CLI 4.214.1 업데이트 후 문제가 생겼다고 물었습니다. 4.214.1이 [SwifterPM](https://tuist.dev/blog/2026/07/16/swifterpm)을 쓰기 시작한 것인지, 아니라면 어떻게 끄고 확인해 볼 수 있는지가 질문이었습니다.

이 사용자는 `TUIST_USE_SWIFTERPM=0`을 찾아서 꺼 봤고, 사내 JFrog 레지스트리에 올려 둔 바이너리 릴리즈를 SwifterPM이 처리하지 못하는 것으로 보인다고 했습니다. 다만 저는 SwifterPM이 4.214.1 이전부터 기본값이라고 답했고, 이 사용자는 4.202.2를 쓰고 있었습니다. `tuist install`은 4.202.0부터 기본으로 SwifterPM을 씁니다. 그러면 4.214.1에서 바뀐 다른 무언가가 원인일 수 있습니다.

원인은 아직 모릅니다. 에러 내용을 스레드에 남기면 Tuist 팀이 볼 수 있다고 안내했는데, 이 사용자는 다른 급한 일이 생겨 테스트를 이어 가지 못했다고 합니다. 

**Hands-on notes: mise의 `packslip:` 설치가 다시 안 됩니다**

지난 호의 packslip 설치를 다시 해 봤습니다. `@latest`는 date filter에 걸려 버전을 찾지 못했고, 4.215.0으로 고정하면 지난 호의 4.214.0 때와 같은 에러가 났습니다. GitHub에서 확인하면 4.215.0 릴리즈에도 `packslip.sigstore.json`이 올라와 있습니다.

```bash
mise use packslip:github.com/tuist/tuist@latest
```

```
✗ packslip:github.com/tuist/tuist@latest  338ms · failed: no versions found for packslip:github.com/tuist/tuist matching date filter
✗ installed 0 tools · 1 failed in 346ms
mise ERROR Failed to install packslip:github.com/tuist/tuist@latest: no versions found for packslip:github.com/tuist/tuist matching date filter
mise ERROR Version: 2026.9.5 macos-arm64 (2026-09-10)
mise ERROR Run with --verbose or MISE_VERBOSE=1 for more information
```

```bash
mise use packslip:github.com/tuist/tuist@4.215.0
```

```
✗ packslip:github.com/tuist/tuist@4.215.0  332ms · failed: github.com/tuist/tuist has no release 4.215.0 carrying packslip.sigstore.json; mise installs from a packslip and does not guess at release assets
✗ installed 0 tools · 1 failed in 340ms
mise ERROR Failed to install packslip:github.com/tuist/tuist@4.215.0: github.com/tuist/tuist has no release 4.215.0 carrying packslip.sigstore.json; mise installs from a packslip and does not guess at release assets
mise ERROR Version: 2026.9.5 macos-arm64 (2026-09-10)
mise ERROR Run with --verbose or MISE_VERBOSE=1 for more information
```

4.214.2로 고정하면 설치됩니다. 다만 프로젝트의 `.mise.toml`에는 4.214.2가 기록되었는데도 4.215.0이 없다는 경고가 함께 나왔습니다.

```bash
mise use packslip:github.com/tuist/tuist@4.214.2
```

```
mise ~/Projects/leesam/loahelper/src/loahelper/.mise.toml tools: packslip:github.com/tuist/tuist@4.214.2
mise WARN  missing: tuist@4.215.0
```

이후 `@latest`를 다시 실행하니 같은 설정으로 전에는 4.214.2가 설치됐는데 이번에는 실패했습니다. 원인은 확인하지 못했습니다.

Pedro님이 Mise 버그 같다며 확인해보겠다고 했는데 이후 소식은 없네요..
