---
name: tuist-changelog-collect
description: Use when starting a new Weekly Tuist News issue and need to gather this period's Tuist release notes, version info, and community highlights. Triggers on "이번 호 소식 모아줘", "체인지로그 수집", "새 릴리즈 확인" or similar.
---

# Tuist 체인지로그 수집

## 소스
- 공식 체인지로그: `tuist.dev/changelog`
- 필요시 GitHub 릴리즈: `github.com/tuist/tuist/releases`
- 공개 Tuist Slack 커뮤니티 (해당 채널에서 다룰 만한 논의/공지)

## 절차
1. 직전 호 발행일 이후 새로 올라온 체인지로그 항목만 수집 (직전 발행일은 사용자에게 물어보거나 이전 이슈 파일에서 확인)
2. 항목별로 기록:
   - 날짜
   - 버전 번호
   - **버전 분류: 하이픈이 없으면 stable, 있으면(`-rc`, `-canary` 등) pre-release.** 이 판별은 기계적으로 함 (예: `4.202.0` → stable, `4.202.0-rc.1` → pre-release)
   - 공식 패치노트 permalink (예: `tuist.dev/changelog/2026.07.16-swifterpm-default`)
3. Pre-release 항목도 수집 대상에 포함 가능. 단, 초안 단계에서 "아직 pre-release 단계"임을 반드시 명시하도록 플래그를 남길 것
4. Slack 커뮤니티 하이라이트: 다룰 만한 게 없으면 억지로 채우지 않음 — 빈 상태로 다음 단계에 넘김 (초안 단계에서 "이번 호는 Tuist Talk 섹션 생략" 처리)
5. 수집 결과를 다음 단계(`tuist-newsletter-draft`)에 넘기기 좋은 형태로 정리 (항목별 날짜/버전/링크/원문 요약)

## 주의
- 이미 지난 호에서 다룬 항목은 중복 수집하지 않음 — 직전 이슈 파일과 대조
- 버전 번호를 추측하지 말 것. 공식 체인지로그/릴리즈 페이지에 명시된 값만 사용. 불확실하면 "버전 미확인"으로 남기고 사용자에게 확인 요청
