---
name: tuist-changelog-collect
description: Use when starting a new Weekly Tuist News issue and need to gather this period's Tuist release notes, version info, and community highlights. Triggers on "이번 호 소식 모아줘", "체인지로그 수집", "새 릴리즈 확인" or similar.
---

# Tuist 체인지로그 수집

## 소스 (전부 매번 확인 — 하나라도 빼먹지 말 것)
- **CLI 릴리즈**: `gh release list --repo tuist/tuist --json tagName,publishedAt,isPrerelease --limit 200` (아래 "CLI 릴리즈 확인 방법" 필수 준수)
- 공식 체인지로그(서버/대시보드 기능, CLI 버전과 무관하게 배포됨): `tuist.dev/changelog`
- 공식 블로그(CEO/팀 글 — Tuist Talk 후보): `tuist.dev/blog`
- 공개 Tuist Slack 커뮤니티 (해당 채널에서 다룰 만한 논의/공지)

### CLI 릴리즈 확인 방법 — WebFetch로 releases 페이지 읽지 말 것
`github.com/tuist/tuist/releases` 페이지를 WebFetch로 요약시키면 **최근 canary 노이즈에 묻혀 중간의 stable 태그를 놓친다** (실제로 4.206.0, 4.207.0을 이 방식으로 두 번 놓쳤음). 반드시 `gh` CLI로 태그 문자열을 직접 필터링:

```bash
# stable만 (하이픈 없는 태그) — 직전 호 발행일 이후
gh api repos/tuist/tuist/releases --paginate \
  --jq '.[] | select(.tag_name | test("^4\\.[0-9]+\\.[0-9]+$")) | "\(.published_at)\t\(.tag_name)"' \
  | sort | awk -F'\t' -v cutoff="<직전 발행일 ISO>" '$1 >= cutoff'
```
`--limit`/`--paginate` 없이 상위 몇 개만 보면 canary가 대부분이라 stable이 빠질 수 있음. 반드시 페이지네이션 끝까지.

## 절차
1. 직전 호 발행일 이후 새로 올라온 항목만 수집 (직전 발행일은 사용자에게 물어보거나 이전 이슈 파일에서 확인)
2. 항목별로 기록:
   - 날짜
   - 버전 번호 (CLI 항목인 경우만 — 체인지로그/블로그 항목은 버전이 없을 수 있음, 정상)
   - **버전 분류: 하이픈이 없으면 stable, 있으면(`-rc`, `-canary` 등) pre-release.** 이 판별은 기계적으로 함 (예: `4.202.0` → stable, `4.202.0-rc.1` → pre-release). **주의: 이 stable/pre-release 분류는 CLI 버전 문자열 전용 기준.** `tuist.dev/changelog`의 서버·대시보드 기능 글은 CLI 버전 번호가 아예 없는 게 정상이며, "CLI에 shipped 됐는지"와는 무관하게 그 자체로 이미 공개(live) 상태 — 별도로 "CLI stable/pre-release"인지 헷갈리지 말 것
   - 공식 패치노트 permalink (체인지로그: `tuist.dev/changelog/...`, CLI: GitHub 릴리즈 노트 URL, 블로그: `tuist.dev/blog/...`)
3. Pre-release 항목도 수집 대상에 포함 가능. 단, 초안 단계에서 "아직 pre-release 단계"임을 반드시 명시하도록 플래그를 남길 것
4. Slack 커뮤니티 하이라이트: 다룰 만한 게 없으면 억지로 채우지 않음 — 빈 상태로 다음 단계에 넘김 (초안 단계에서 "이번 호는 Tuist Talk 섹션 생략" 처리)
5. 수집 결과를 다음 단계(`tuist-newsletter-draft`)에 넘기기 좋은 형태로 정리 (항목별 날짜/버전/링크/원문 요약)
6. **사용자가 "가장 중요한 N개 뽑아줘" 요청 시**: 후보 전체(CLI + 체인지로그 + 블로그 + Slack)를 놓고 뽑을 것 — 체인지로그 항목만 보고 CLI/블로그를 빼먹지 말 것. 선정 기준은 이 뉴스레터 독자(1인/소규모 iOS 엔지니어)에게 실질 영향이 큰 것 우선 — 무료 티어 제한, 기본 동작 변경, 캐시/빌드 체감 성능처럼 직접 와닿는 것이 Gradle/Bazel/Buildkite 같은 엔터프라이즈·타 플랫폼 도구 연동보다 우선순위 높음. 이유를 한 줄로 남길 것

## 주의
- 이미 지난 호에서 다룬 항목은 중복 수집하지 않음 — 직전 이슈 파일과 대조
- 버전 번호를 추측하지 말 것. 공식 체인지로그/릴리즈 페이지에 명시된 값만 사용. 불확실하면 "버전 미확인"으로 남기고 사용자에게 확인 요청
- 소스 4개(CLI 릴리즈/체인지로그/블로그/Slack) 중 하나라도 확인 안 하고 "수집 완료"라고 보고하지 말 것
