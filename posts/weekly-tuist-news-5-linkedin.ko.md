📦 Weekly Tuist News #5 발행했습니다.

이번 호는 CLI 4.210.0부터 4.212.0까지 3주치 릴리즈와, 제가 직접 재본 빌드 시간 비교를 담았습니다.

- Tuist 저장소의 GitHub Actions가 매주 월요일에 RC를 stable로 승격하도록 자동화됐고, Xcode 27의 project.xcproj를 켜는 `projectFormat` 옵션이 4.210.0부터 stable CLI에 들어왔습니다
- 4.211.0과 4.212.0은 캐시 키, 모듈 캐시, 캐시 복원, generate 속도를 다듬는 수정이 중심입니다
- 같은 프로젝트(Xcode 27.1)에서 4.207과 4.212를 비교하니 install 18s에서 10.2s, cache 54s에서 43s, clean build 7.5s에서 3.7s로 줄었습니다. generate는 2.7s에서 3.3s로 오히려 늘었습니다

개인이 운영하는 뉴스레터이고, Tuist 공식 역할은 없습니다. 회사 프로덕션에서는 아직 Tuist를 쓰지 않습니다.

전체 글: https://toyboy2.medium.com/weekly-tuist-news-5-d513c639abb7

공식 체인지로그가 놓친 이야기들은 지난 호에서 확인할 수 있어요: https://toyboy2.medium.com/list/weekly-tuist-news-740a616cf8f3

#Tuist #iOS #Xcode #SwiftPM #빌드시스템 #뉴스레터
