---
name: tuist-newsletter-format
description: Use when assembling the final Weekly Tuist News markdown file, the LinkedIn summary post, or the cover image prompt — i.e. turning approved draft content into publish-ready output. Triggers on "최종 포맷으로 만들어줘", "링크드인 포스트도", "커버 이미지 프롬프트", "발행용으로 정리" or similar.
---

# Weekly Tuist News 최종 포맷

## 전제 조건
이 스킬은 한국어(그리고 승인된 경우 영어) 초안이 **이미 확정된 뒤**에만 사용한다. 초안이 미확정이면 `tuist-newsletter-draft` 스킬로 먼저 돌아간다.

## Medium 아티클 구조
```
# Weekly Tuist News #N
*A catch-up roundup of what's happening in the Tuist ecosystem — release notes, community highlights, and notes from actually using it.*

🇰🇷 한국어 버전은 아래로 스크롤해주세요.
---
## English
[전체 영문 섹션]
---
## 한국어
[전체 한글 섹션]
```

### 언어 배치 규칙
- **섹션별 영/한 병기 금지.** English 전체 → 한국어 전체 순서로 완전히 분리한다.
- Medium은 커스텀 앵커 링크(`[텍스트](#한국어)`)를 지원하지 않는다 — 사용 금지. 안내 문구("한국어 버전은 아래로 스크롤해주세요")만 쓴다. 헤더만 제대로 잡으면 Medium 자체 목차 기능이 자동으로 동작한다.

## LinkedIn 요약 포스트
- Medium 링크 + 핵심 3줄 요약 + 정직한 포지셔닝 한 줄 + 해시태그
- 한국어 메인, 필요하면 영어 버전도 별도 작성
- 본문 뉴스레터와 별도 파일로 산출

## 이미지
- **커버 이미지**: 시리즈 전체에서 같은 스타일 템플릿 유지 (매호 새로 디자인하지 않음)
  - 텍스트나 정확한 레이아웃(로고 자리 등)이 필요하면 AI 이미지 생성보다 **Canva/Figma 권장** — 디퓨전 모델은 텍스트 렌더링과 정확한 여백 지정에 약함
  - AI 프롬프트를 쓸 경우: Tuist 공식 로고를 프롬프트에 넣지 않는다 (상표권 문제) — 로고는 공식 홈페이지에서 받아 별도로 합성
  - 프롬프트 글자 수 제한이 있는 도구면 확인 후 맞춰서 압축
- **본문 삽화**: 영어 섹션에만 포함 (스크린샷/터미널 로그 등 — 기술적 증거로서 유효). 한국어 섹션은 빠른 스캔 우선으로 이미지 생략. 텍스트 없는 순수 다이어그램은 양쪽에 재사용 가능

## 발행 전 체크리스트
- [ ] 한국어 승인 완료 확인
- [ ] 영어가 한국어 승인 이후에 작성되었는지 확인 (내용 추가 없이 번역만 되었는지)
- [ ] 패치노트 링크 전부 살아있는지
- [ ] Pre-release 항목에 라벨 명시되어 있는지
- [ ] Tuist Talk 섹션 — 내용 없으면 생략되어 있는지
- [ ] 한국어 프로즈에 em-dash 없는지 (코드블록 제외)
