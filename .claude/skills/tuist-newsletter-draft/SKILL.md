---
name: tuist-newsletter-draft
description: Use when writing or revising the Weekly Tuist News draft content (Korean or English body text, release summaries, personal experience notes). Triggers on "초안 써줘", "한국어로 정리", "도입 후기 추가", "이 내용으로 수정" or similar drafting/editing requests for the newsletter.
---

# Weekly Tuist News 초안 작성

## 순서 (절대 규칙)
1. **한국어 초안을 먼저 작성**한다.
2. 사용자 검토 → 명시적 승인("확인했습니다", "이 버전 확정" 등)을 받는다.
3. 승인된 한국어를 기준으로만 **영어 번역**을 작성한다. 한국어 승인 전에는 영어를 작성하지 않는다.

## 내용 추가 여부 판단
- 사용자가 "그대로 정리만", "내용 추가하지 말고" 라고 하면: 포맷팅(헤더, 볼드, 코드블록)만 하고 문장·설명·링크를 새로 추가하지 않는다.
- 사용자가 구체적 내용(경험담, 인용 등)을 붙여넣으면: 그 내용을 그대로 반영하되, 오타 수준의 명백한 실수(예: 실행 안 되는 명령어 오타)만 바로잡고 그 사실을 짧게 알린다.
- 새로운 캐veat이나 설명 문장을 자발적으로 추가하고 싶으면, 먼저 제안하고 사용자 승인을 받은 뒤 반영한다 (임의로 넣지 않는다).

## 섹션별 콘텐츠 규칙

### 소개 / Who's writing this
- 자기소개 + 정직한 포지셔닝(회사 프로덕션 미사용, 앰배서더 비공식)
- 창간호는 온전히, 이후 호부터는 축소나 위치 이동 고려 (사용자와 상의)

### 릴리즈 소식
- 항목별: 제목(볼드) + 본문 + 패치노트 링크
- Pre-release 항목은 소제목이나 본문 첫 문장에 "아직 pre-release/canary 단계" 명시
- 버전 표기는 하이픈 없는 stable만 "출시됨"으로 서술. Pre-release는 반드시 그렇게 라벨링

### Tuist Talk (커뮤니티 섹션)
- 그 호에 다룰 내용이 없으면 섹션 자체를 생략 (빈 placeholder로 남기지 않음)
- Slack 발언 인용 시 출처(스레드 링크) 명시, 과도한 직접 인용 대신 재구성

### 도입 후기 / Hands-on notes
- 실제로 해본 것만, 실패도 숨기지 않고 포함
- 터미널 로그나 에이전트 응답을 인용할 때는 번역하지 않고 코드블록으로 원문 그대로 유지 (영/한 양쪽 섹션 동일)

## 언어별 표기 규칙
- **한국어 프로즈**: em-dash(—) 대신 괄호() 사용. 단, 코드블록/로그 인용 내부는 원문 그대로 두고 바꾸지 않음
- **영어**: em-dash 정상 사용
- CLI 명령어, 버전 번호, 코드는 번역하지 않음
