# Weekly Tuist News #1

*Tuist 생태계 소식 모음 — 릴리즈 노트, 커뮤니티 하이라이트, 그리고 직접 써보며 정리한 후기.*

발행: Medium, Lee young-jun (toyboy2) — https://toyboy2.medium.com/weekly-tuist-news-1-da9f701453e5

> Note: 발행된 Medium 아티클에서 가져온 로컬 아카이브입니다. 인라인 스크린샷("Press enter or click to view image in full size")은 포함하지 않음 — 텍스트만.

---

### 소개

iOS 개발자 이영준(게임도우미)입니다. Tuist 한국어 페이지 번역에 기여했고, Tuist 공식 홈페이지에 제 후기가 대표로 올라가 있습니다. 다만, 회사 프로젝트에서 Tuist를 써본 적은 아직 없습니다. (지금까지 팀들은 React Native였거나 도입 타이밍을 못 잡았거든요.) 대신 개인 프로젝트에는 전부 Tuist를 쓰고 있고, 공개 Tuist Slack 커뮤니티도 꾸준히 지켜보고 있습니다.

이 시리즈는 빠르게 업데이트 되는 Tuist를 꾸준히 따라가며 변경 사항들을 공유하려는 개인 프로젝트입니다.

발행 주기에 대해 말씀 드리자면: Tuist는 매주 정확히 릴리즈되는 편이 아니라서, 이번 창간호는 엄밀한 "일주일"이 아니라 최근 2주 정도의 주요 소식을 모아보는 형태로 구성했습니다. 2호부터는 직전 호 이후 기간을 기준으로 하되, 소식이 적으면 짧게, 많으면 길게 가겠습니다.

### 지난 주 주요 변경사항

**1. SwifterPM을 기본 의존성 복원 방식으로 채택**

4.202.0부터 `tuist install`이 기본적으로 SwifterPM을 사용해서 Swift 패키지 의존성을 가져오고 복원합니다. 패키지 해석(resolution) 자체는 여전히 Swift Package Manager가 담당하지만, 복원(restoration) 단계에서 각 worktree에 파일을 복사하는 대신 전역 콘텐츠 주소 기반 저장소에 Symbolic 링크만 겁니다. 결과적으로 캐시가 미리 준비된 상태에서의 복원은 1초 미만으로 줄고, 여러 worktree에 중복 저장되어 발행한 용량 문제도 사라집니다. 기존에는 선택사항이었는데 이제는 기본으로 사용되고, 지원 안 되는 패키지를 사용하면 환경 변수로 되돌릴 수 있습니다. (`TUIST_USE_SWIFTERPM=0`)

팁: CI에서는 `~/.cache/swifterpm`을 캐시에 등록해 사용할 수 있습니다.

**2. Target 캐시 무효화 조건 제어**

Target의 캐시는 소스 파일과 선언된 의존성 외에도 코드 생성 템플릿, 툴 버전, 설정 파일, 환경변수 등에 영향을 받을 수 있습니다. 지금까지는 이런 환경이 바뀌어도 Tuist 캐시가 무효화되지 않는 위험이 있었지만, 이제 Pre-release 4.204.0 부터 Target에 `additionalHashingInputs`를 설정해서 제어할 수 있습니다.

**3. 코딩 에이전트가 Tuist에 직접 인증 가능 (auth.md)**

7월 17일부터 Tuist의 MCP 서버로 연결하는 Agent를 위해 `auth.md`를 지원합니다 (현재 Claude Code, Codex, OpenCode, Pi를 지원합니다). 인증되지 않은 Agent가 인증 방법을 스스로 찾아내고, 등록하고, 6자리 승인 코드를 요청한 뒤, 사용자가 코드를 페이지에 입력하면 범위가 제한된 자격증명을 받는 방식입니다. 연결되면 에이전트가 계정 목록 조회, 프로젝트 생성, Gradle 연동 가이드, 캐시 동작 검증까지 수행하고 완료를 보고합니다.

팁: MCP는 인증되지 않은 상태로 나오지만 동작하는 상태가 됩니다.

### Tuist Talk

Tuist에서 자체적으로 Github용 macOS runner (`tuist-macos`)를 테스트해보고 싶으면 DM을 달라는 공지가 있어서 초대 받아 시도해봤지만 동작하지 않았습니다.

상담 결과 조직(Organization)에서 시도해보라는 답변을 받았지만 프로젝트를 조직으로 옮길 수는 없어 아직 사용할 수 없는 상태입니다.

테스트 해보고 싶으면 runner를 변경 후 DM이나 댓글로 요청해보세요.

### 도입 후기

**SwifterPM**

SwifterPM가 기본으로 설정되었다는 것을 본 후 제 프로젝트에서 사용할 수 있는지 얼마나 빨라지는 것인지 궁금했습니다.

비교 자료를 만들기 위해 기존 Branch에서 Github 배포를 실행하고 Tuist를 업그레이드 한 Branch에서 다시 한번 시도했습니다.

그러나 빌드가 실패 되었고 문제를 찾기 위해 로컬에서 빌드를 시도하니 모든 Core Data의 Model들을 찾지 못한다는 오류가 발생하고 있었습니다.

Resources에서 모델 파일이 누락되어있었습니다. 이전 Article에 "Multiple commands produce" 오류를 해결하기 위해 Project Manifest에서 xcdatamodeld에 있는 .xcdatamodel 파일을 제외 시켰는데 그것이 원인이었습니다.

해당 xcdatamodeld는 지난 업데이트에서 Scheme가 업데이트 되어 Model.xcdatamodel, Model2.xcdatamodel 두 개가 있었는데 Model.xcdatamodel만 제외하도록 하니 해결이 되었습니다.

하지만 뭔가 깔끔하지 않아 보였고 새로운 옵션을 시도했습니다.

이런 오류 때문에 coreDataModels가 일찍이 추가되었지만, 제가 저 오류를 해결하기 위해 시도할 때는 문제를 해결해주지 못했습니다.

Resources에서 xcdatamodeld를 제외한 상태를 유지하고, coreDataModels를 제외한 xcdatamodeld로 지정하니 오류가 사라졌습니다.

이전에 오류가 발생했을 때는 어떤 버전에서 발생하는 문제인지 어떤 Commit이 문제인지 탐색해서 찾아냈는데 이번에는 그렇게 까지 찾지는 않았습니다.

이렇게 빌드 오류를 해결하고 배포 소요 시간을 확인하니 전혀 달라진 것이 없어서 이번에도 로컬에서 확인하기 시작했습니다.

SwifterPM의 안내를 보면 ~/.cache/swiftpm에 캐시가 생성되기 때문에 이 경로를 캐시로 등록해두라고 되어있었으나 전혀 생성이 되지 않았습니다.

Tuist 캐시 뿐 아니라 SPM의 캐시까지 모두 날려도 동작하지 않았고 Package.swift 사용 시에만 동작하는 것이라는 가설을 생각했습니다.

이전에 만들어둔 Tuist Sample을 받아 tuist install을 실행하니 로그에서 swifterpm을 확인할 수 있었고, clean 명령어 후 다시 install을 실행했을 때 내려받는(fetching) 대신 ~/.cache/swiftpm 경로로 복구(restored) 되었다는 로그를 확인할 수 있었습니다.

"XcodeProj 기반으로 통합 했다면 tuist install 명령 실행 시에는 SwifterPM을 사용한다"는 설명을 보고 Tuist로 만들어진 프로젝트에서도 동작할 것으로 기대했으나 확인 결과 Package.swift를 사용하는 프로젝트에서만 동작합니다.

**auth.md**

테스트를 위해 먼저 기존 로그인을 해제했습니다.

```bash
mise x -- tuist auth logout
```

Agent에게 generate를 하도록 했을 때 로그인하라는 오류를 확인했습니다.

Tuist MCP를 설치 후

```bash
claude mcp add --transport http tuist https://tuist.dev/mcp
```

다시 시도하니 아래와 같이 인증이 필요하다고 나옵니다:

> Still unauthenticated. A Tuist MCP auth tool just became available — trying that instead of the browser flow:
> That MCP tool authenticates the MCP server, not the CLI — it won't write the ~/.config/tuist/credentials entry that tuist generate reads. Wrong lever; skipping it.
> Checking whether the auth requirement can be sidestepped for a plain generate:
> Ran 1 shell command
> Can't be worked around from here. Tuist.swift declares fullHandle: "gamehelper/democracy-action-ios", so the project is server-connected and every generate authenticates first — --no-binary-cache fails identically.
> The login is an interactive browser flow, so it has to come from your terminal:
> ! mise x -- tuist auth login
> Ping me after and I'll run the generate.

MCP를 인증 후에도 여전히 로그인을 요청해서, Tuist 빌드에 대한 인증을 자동화 하는 것은 아니라고 생각했습니다. (설명에 있었는데 제대로 읽지 않은 것 같네요)

MCP 인증을 해제한 뒤 프로젝트 목록을 달라니 이메일 주소를 알려달라고 했습니다. 이메일 주소를 알려줬더니 인증 코드를 발급하고 페이지를 열어서 입력했습니다.

해당 페이지에 접속하니 6자리 Code를 입력 하라고 나왔고, 인증을 완료하니 Claude가 감지 후 프로젝트 목록을 정상적으로 표시하는 것을 확인할 수 있었습니다.

하지만 MCP는 여전히 failed 상태로 남아있었는데요. 다시 인증했을 때 브라우저를 열고 인증 후에 자동으로 닫는 것을 확인할 수 있었습니다.

MCP 로그아웃 후에 동작했기 때문에 이 인증을 하면 MCP에 연결 되는 것을 기대 했지만, MCP는 여전히 연결되지 않은 상태로 나타나지만 정상 호출되는 상황이 혼란스러웠습니다.

auth.md를 통해 인증하면 Agent는 Token을 가지고 있지만 CLI는 가지고 있지 않아서 이런 현상이 발생하는 것으로 생각합니다.
