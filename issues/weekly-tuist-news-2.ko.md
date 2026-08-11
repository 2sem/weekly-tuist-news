# Weekly Tuist News #2 (초안 — 검토 필요)

> 이 파일은 초안입니다. 검토 후 승인하면 영어 번역 및 최종 포맷 단계로 진행합니다.
> 수집 범위: 1호가 다룬 마지막 항목(additionalHashingInputs, 2026-07-28) 이후 ~ 2026-08-11 기준 tuist.dev/changelog.
> 소개(Who's writing this) 섹션은 이번 호부터 생략.

### 지난 소식 (1호 이후)

**1. CI에서 실행 결과를 JSON으로 바로 받기 (`--run-report-path`)**

지금까지는 CI 잡에서 대시보드 URL을 얻으려면 로그를 읽거나(GitHub Actions는 job summary로 해결됐지만) 다른 CI에서는 로그 텍스트를 정규식으로 파싱해야 했습니다(문구나 URL 형식이 바뀌면 바로 깨지는 방식). `tuist test`, `tuist xcodebuild test`, `tuist xcodebuild build`에 `--run-report-path`(또는 환경변수 `TUIST_RUN_REPORT_PATH`) 옵션이 추가되어, 실행 결과를 JSON 리포트로 저장할 수 있습니다. 빌드와 테스트를 동시에 하는 커맨드라면 두 URL이 모두 리포트에 포함됩니다.

```json
{
  "runId": "...",
  "status": "success",
  "runURL": "https://tuist.dev/acme/app/runs/123",
  "testRunURL": "https://tuist.dev/acme/app/tests/456",
  "buildRunURL": "https://tuist.dev/acme/app/builds/789",
  "testRuns": [
    { "scheme": "App", "succeeded": true, "totalTests": 10, "skippedTests": 2, "ranTests": 8, "failedTestNames": [] }
  ],
  "buildRuns": [{ "scheme": "App", "succeeded": true, "durationInSeconds": 432 }]
}
```

4.203.0(stable)에 포함되어 출시됨. 패치노트: https://tuist.dev/changelog/2026.07.17-run-report-json

**2. 네트워크 재시도 정책을 직접 조정 가능**

지금까지는 실패한 요청마다 재시도 횟수가 3회로 고정돼 있었습니다(타임아웃을 늘리면 한 번의 지연이 최대 네 번의 긴 대기로 이어질 수 있었음). 이제 환경변수로 재시도 정책을 조정할 수 있습니다.

- `TUIST_HTTP_MAXIMUM_RETRY_COUNT`(기본 3, 0이면 재시도 비활성화, 최대 10)
- `TUIST_HTTP_RETRY_BASE_DELAY_IN_MILLISECONDS`(기본 100ms, 최대 30000ms)

참고로 기존 기본 딜레이가 원래 의도(100ms)보다 훨씬 짧은 약 1ms로 동작하던 버그도 이번에 같이 수정됐습니다. 재시도마다 딜레이가 두 배로 늘고 무작위 지터가 더해져, 네트워크 장애 시 클라이언트들이 한꺼번에 재시도하는 상황을 피할 수 있습니다.

4.203.0(stable)에 포함되어 출시됨. 패치노트: https://tuist.dev/changelog/2026.07.21-configurable-network-retries

**3. Quarantine/Flaky 테스트를 "언제 표시됐는지" 기준으로 정렬**

Quarantined 테스트 페이지에 "Quarantined at" 컬럼이, Flaky 테스트 페이지에 "Marked flaky at" 컬럼이 추가되어 정렬할 수 있게 됐습니다. 가장 오래 방치된 테스트를 찾거나, 최근에 무엇이 격리됐는지 시간순으로 확인하기 쉬워졌습니다.

(CLI 버전과는 무관한 대시보드 기능입니다.) 패치노트: https://tuist.dev/changelog/2026.08.04-sort-quarantined-flaky-tests-by-marked-time

---

### Tuist Talk

**Tuist Slack에 새 후기 모집 공지 (7/29)**

Tuist 팀이 Slack 커뮤니티에 웹사이트 리브랜딩을 준비 중이라며 사용자 후기를 모집하는 글을 올렸습니다. 빌드 속도 개선, 개발 경험 향상, 프로젝트 유지보수 편의 등 무엇이든 좋으니 Tuist가 실제로 도움이 된 부분을 몇 문장이라도 공유해달라는 내용입니다. 관심 있으면 Slack 댓글이나 DM으로 회신하면 됩니다.

참고로 Tuist 팀의 Pedro는 작년에 한국 iOS 개발자들이 Tuist 노하우를 공유하는 오픈 카카오톡방에 초대되어 비슷한 후기 요청 글을 올린 적이 있는데, 그때 남긴 제 후기가 지금도 Tuist 공식 홈페이지에 실려 있습니다.

*(스크린샷 추가 예정)*

### 도입 후기

*(아직 미정 — 아래 질문 참고)*
