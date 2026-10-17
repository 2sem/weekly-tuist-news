# Weekly Tuist News #5

> Note: 한국어 초안 승인됨. 아직 Medium에 발행되지 않았습니다.

---

이번 호는 눈에 띄는 기능보다는 캐시 정확도와 생성 속도를 다듬는 수정이 대부분입니다. 한편 Xcode 27의 `project.xcproj` 옵션은 canary에만 있다가 4.210.0부터 stable CLI에 들어왔습니다. 대시보드 쪽은 캐시 전송량과 무료 티어 사용량 관련 업데이트가 있었습니다.

**1. CLI 4.210.0 (9/28): 월요일 정기 릴리즈 자동화, Xcode 27 프로젝트 포맷 옵션**

Tuist 저장소의 GitHub Actions가 매주 월요일에 RC를 stable로 승격하고 이어서 다음 RC를 만들도록 자동화되어, CLI 릴리즈 주기가 빨라졌습니다.

기능으로는 `Tuist.swift`에서 Xcode 27.2의 JSON5 기반 프로젝트 포맷(`project.xcproj`)을 켜는 `projectFormat` generation option이 추가됐습니다. 그 밖에 캐시 warm 때 그래프를 두 번 해시하던 것을 한 번으로 줄인 성능 개선, 패키지 번들 식별자에서 밑줄(`_`)이 사라지던 문제 수정이 있습니다.

원문: [4.210.0 릴리즈 노트](https://github.com/tuist/tuist/releases/tag/4.210.0)

**2. CLI 4.211.0 (10/5): 캐시 키와 모듈 캐시 정확도**

추가된 기능은 둘입니다. 하나는 모던 모듈 캐시가 REAPI(Remote Execution API)로 옮겨간 것이고, 다른 하나는 CI 제공자 전반에서 쓸 수 있는 Linux 러너 캐시 볼륨입니다. 나머지는 수정이 중심입니다.

- 바이너리 캐시 키에 Xcode 빌드 정보를 포함하도록 수정됐습니다.
- 캐시 키 해시에 warnings-as-errors 설정이 섞여 들어가던 것이 빠졌습니다.
- 느린 네트워크에서 모듈 캐시 다운로드가 중간에 끊겨도 이어받도록 바뀌었습니다(기존에는 상한을 두고 끊었습니다).
- Xcode 캐시에서 eviction으로 지워진 항목 때문에 조용히 miss가 나던 문제가 수정됐습니다.
- selective testing 해시에 툴체인이 포함되고, 건너뛴 테스트 모듈이 잡 요약에 표시됩니다.
- 새로 추가한 테스트가 불안정할 때 report 모드에서 해당 타겟을 계속 재실행하도록 고쳐졌습니다. 4호에서 다뤘던 `--stress-new-tests` 쪽 수정입니다.

원문: [4.211.0 릴리즈 노트](https://github.com/tuist/tuist/releases/tag/4.211.0)

**3. CLI 4.212.0 (10/12): 생성 속도와 캐시 복원 수정**

- 생성(generate) 속도: 생성 단계의 부수 작업을 동시에 실행하고 O(n²)였던 링크 검사를 없앴으며, 모든 타겟의 클로저를 순회하던 `StaticXCFrameworkModuleMapGraphMapper`의 작업도 줄였습니다. 로컬 캐시 히트 시 서명 검증 때문에 generate가 느려지던 문제도 고쳐졌습니다.
- 캐시 복원: 출력에 빈 파일이 있는 캐시 타겟이 복원되지 않던 문제, 4.211 cold generate에서 모듈 캐시가 멈추던 문제, 릴리즈용으로 warm한 XCFramework에 dSYM이 번들되지 않던 문제가 수정됐습니다.
- 그 외: Swift 경고 그룹 플래그 보존, 직접 쓴 test plan이 있는 scheme에서 selective testing이 제대로 도는 것, Xcode 27 cache prefix mapping을 켰을 때 코드 커버리지가 유지되는 것이 있습니다.

원문: [4.212.0 릴리즈 노트](https://github.com/tuist/tuist/releases/tag/4.212.0), [4.211.1 릴리즈 노트](https://github.com/tuist/tuist/releases/tag/4.211.1)

**4. Xcode 캐시가 바뀐 부분만 주고받습니다**

소스 몇 줄만 고쳐도 컴파일 산출물 전체가 새로 올라가던 부분이 개선됐습니다. Xcode 캐시가 content-defined chunking을 쓰도록 바뀌어서, 업로드할 때는 서버에 없는 chunk만 보내고 다운로드할 때는 로컬에 남아있는 chunk를 재사용한 뒤 빠진 조각만 받아서 원본을 복원합니다. 이 로컬 chunk 캐시는 `DerivedData`를 지우거나 캐시 서비스를 재시작해도 남아있습니다.

Tuist가 공개한 자체 벤치마크(생성된 픽스처 기준)로는 C 함수 하나를 고쳤을 때 해당 오브젝트의 전송량이 47.4% 줄었고, 큰 Swift 모듈에서 프로퍼티 이름 하나를 바꿨을 때 27.8% 줄었습니다. 전송 바이트 기준이지 빌드 시간 자체를 그만큼 줄인다는 뜻은 아니고, 컴파일러의 invalidation 동작도 그대로입니다.

원문: [Reuse unchanged chunks when sharing Xcode build outputs](https://tuist.dev/changelog/2026.09.08-xcode-cache-chunk-reuse)

**5. Air 사용량이 한도에 가까워지면 메일이 옵니다**

지난 호에서 무료 요금제(Air)의 캐시 사용량 제한이 적용되기 시작했다는 소식을 전했는데, 이번엔 그 한도에 도달하기 전에 알려주는 기능이 붙었습니다. 월간 캐시 또는 러너 사용량의 80%와 100% 지점에서 조직 관리자와 개인 계정 소유자에게 메일이 갑니다. 메일에는 현재 사용량, 리셋 날짜, Pro 업그레이드 링크가 들어있습니다.

원문: [Get an email before reaching your Air limits](https://tuist.dev/changelog/2026.09.09-air-usage-emails)

### Tuist Talk

[Slack](https://tuist-community.slack.com/archives/C018QG7U7SN/p1789582255849589)에서 한 사용자가 "이건 흥미롭겠는데"라며 이 얘기를 꺼내자 Pedro는 요즘 모델들이 `.pbxproj`의 형식과 구조는 이미 잘 이해한다고 답했습니다. 그 사용자는 그래도 흥미롭긴 한데 늘 그렇듯 Xcode 한두 버전 동안은 버그도 있고 pbxproj 기능을 다 갖추지도 못할 거라고 봤고, Pedro는 Tuist 지원 작업을 이미 하고 있고 오늘내일 중에 나올 거라고 답했습니다.

실제로 [얼마 지나지 않아](https://tuist.dev/changelog/2026.09.17-xcode-27-project-format) 4.210.0부터 `Tuist.swift`의 generation option으로 이 포맷을 켤 수 있게 되었습니다.

```swift
import ProjectDescription

let tuist = Tuist(
    project: .tuist(
        generationOptions: .options(
            projectFormat: .xcproj
        )
    )
)
```

다만 포맷 자체는 Xcode에서 아직 실험적인 단계입니다. 기본값은 그대로 `.pbxproj`라서 옵션을 켜지 않으면 달라지는 게 없습니다. 그런데 Tuist를 쓰는 우리는 이거 신경 안 써도 되죠 :)

### Practice

Xcode 27.1 환경에서 같은 프로젝트로 4.207과 4.212의 소요 시간을 비교해 봤습니다.

**4.207**
- install: 18s
- cache: 54s
- generate: 2.7s
- clean build: 7.5s

**4.212**
- install: 10.2s
- cache: 43s
- generate: 3.3s
- clean build: 3.7s

install, cache, clean build는 줄었고, generate는 2.7s에서 3.3s로 오히려 늘었습니다.
