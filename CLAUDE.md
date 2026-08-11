# Weekly Tuist News

이영준(iOS 엔지니어)이 운영하는 개인 뉴스레터. Tuist 릴리즈/커뮤니티 소식을 큐레이션해서 Medium + LinkedIn에 발행.

콘텐츠 파이프라인 저장소다 — 빌드/린트/테스트 명령어 없음. 코드가 아니라 스킬(`.claude/skills/`)과 마크다운 산출물로 구성됨.

## 파이프라인 (3단계, 순서 고정)

1. **수집** — `tuist-changelog-collect`: `tuist.dev/changelog`, GitHub 릴리즈, Slack 커뮤니티에서 지난 호 이후 소식 수집. Stable/pre-release 기계적 판별(하이픈 유무), 버전 번호 추측 금지.
2. **초안** — `tuist-newsletter-draft`: 한국어 먼저 작성 → 사람 승인 → 승인된 한국어 기준으로만 영어 번역. 사용자가 "정리만" 요청 시 포맷팅만, 문장 추가 금지.
3. **포맷** — `tuist-newsletter-format`: 승인된 초안을 Medium 아티클 구조(English 전체 → 한국어 전체, 앵커 링크 금지)로 조립. LinkedIn 요약 포스트는 별도 파일. 커버 이미지는 시리즈 템플릿 유지, Tuist 공식 로고 프롬프트에 넣지 않음.

각 스킬은 트리거 문구로 자동 로드됨 (예: "이번 호 소식 모아줘", "초안 써줘", "최종 포맷으로 만들어줘"). 세부 규칙은 각 `SKILL.md` 참고.

## 절대 규칙 (모든 단계 공통)

- **자동 발행 금지.** 한국어 초안은 반드시 사람이 검토·승인해야 다음 단계(영어 번역)로 진행.
- **한국어 먼저, 영어는 승인 후.** 순서 바꾸지 말 것.
- **정직성 우선.** 회사 프로덕션에서 Tuist 미사용 사실을 숨기지 않음. "앰배서더" 타이틀 직접 주장 금지.
- **내용 추가 지시 없이는 추가하지 않음.** "정리만 해줘"라고 하면 포맷팅만, 문장 추가 금지.
- **Medium 발행은 API로 직접 하지 않는다.** Medium이 2025년부터 신규 API 연동을 막았고 공식 MCP 커넥터도 없음. 대신 GitHub Pages(`docs/`)에 최종 마크다운을 올려 공개 URL을 만들고, Medium → Stories → **Import a story**로 가져온다 (원본 URL이 canonical link로 자동 설정됨). 최종 **Publish 버튼은 항상 사람이 누른다.**
- **Import 후 코드블록은 반드시 사람이 검수한다.** Medium import 과정에서 코드블록 서식이 깨지는 사례가 보고됨 — 자동 검증 불가.

## 향후 확장 (아직 미구현, 착수 시 스킬로 분리)

- `tuist-blog-collect`: 체인지로그 외에 `tuist.dev/blog` 포스트도 수집 대상에 추가.
- `medium-publish-prep`: 확정된 마크다운을 `docs/`에 커밋·푸시하고 GitHub Pages 배포를 확인한 뒤, Medium import 절차를 사람에게 안내. 이 스킬 자체는 아무것도 게시하지 않음.

두 스킬 다 착수 전이며 `docs/` 폴더도 아직 없음. 착수 시 GitHub 저장소/Pages 설정, `gh auth login`이 선행되어야 함(사람이 직접).
