<!-- readme-translate-kr-en:start -->
<p align="right">
  <sub>
    🌐 Language&nbsp;&nbsp;
    <a href="./README.md">English</a>
    &nbsp;|&nbsp;
    <a href="./README.ko.md">한국어</a>
  </sub>
</p>
<!-- readme-translate-kr-en:end -->

# readme-translate-kr-en

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-readme--translate--kr--en-blue?logo=github)](https://github.com/marketplace/actions/readme-translate-kr-en)
[![GitHub release](https://img.shields.io/github/v/release/choiwlsd/readme-translate-kr-en)](https://github.com/choiwlsd/readme-translate-kr-en/releases/latest)
[![CI](https://github.com/choiwlsd/readme-translate-kr-en/actions/workflows/ci.yml/badge.svg)](https://github.com/choiwlsd/readme-translate-kr-en/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./ 라이센스)

영어와 한국어 README 파일을 번역하고 동기화하기 위한 무료 로컬 퍼스트 깃허브 액션입니다.

**npm 패키지나 유료 번역 API가 필요하지 않습니다.** 번역은 오픈 소스 기계 번역 모델이 있는 GitHub 액션 러너에서 실행됩니다.

##  스타트

생성 `.github/workflows/translate-readme.yml` 번역하려는 README가 포함된 저장소에서:

동일한 워크플로우가 작동하는지 여부 `README.md` 영어 또는 한국어로 작성됩니다. 설정하지 마십시오. `from`; 액션은 콘텐츠로부터 소스 언어를 검출하고 반대 언어 파일명을 자동으로 선택한다.

```yaml
name: Sync bilingual README

on:
  push:
    branches: [main]
    paths:
      - README.md
  workflow_dispatch:

permissions:
  contents: write

concurrency:
  group: readme-translation-${{ github.repository }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  translate:
    if: github.actor != 'github-actions[bot]'
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v7
        with:
          fetch-depth: 0

      - name: Translate README
        uses: choiwlsd/readme-translate-kr-en@v0.2.5
        with:
          source-file: README.md
```

### 첫 번째 번역을 생성합니다.

워크플로우 파일을 추가하면 즉시 실행되지 않습니다. `README.md` 변경되지 않았습니다. 워크플로우 파일을 디폴트 브랜치로 커밋하고 푸시한 후:

1. 창고 열어봐. **액션** GitHub의 탭.
2. 선택 **동기화 이중 언어 리드미**.
3. 선택 **실행 워크플로우**, 기본 브랜치를 선택하여 실행하세요.
4. 워크플로가 끝날 때까지 기다립니다. 그것은 지배적인 언어를 감지합니다. `README.md`, 그런 다음 생성, 커밋 및 푸시 `README.ko.md` 영어 소스 콘텐츠 또는 `README.en.md` 한국어 소스 콘텐츠에 대해.

| 소스의 내용물 `README.md` | 생성된 번역 |
| ------------------------------ | --------------------- |
| 영어.                        | `README.ko.md`        |
| 한국어.                         | `README.en.md`        |

리포지토리 간에 소스 언어가 다를 때 워크플로를 변경할 필요가 없습니다. 두 경우 모두 유지합니다. `README.md` 진리의 원천으로서 그리고 정확히 같은 것을 사용 `source-file: README.md` 세팅.

워크플로는 디폴트 브랜치에 존재해야 한다. **실행 워크플로우** 버튼이 있습니다. 누름이 거부되면 열립니다. **설정 → 액션 → 일반 → 워크플로우 권한** 그리고 GitHub 액션이 저장소 콘텐츠를 작성하는 데 허용되는지 확인하십시오. 조직 정책 또는 지점 보호는 여전히 직접 푸시를 방지할 수 있습니다.

첫 번째 번역 후, 나중에 푸시할 때마다 변경됩니다. `README.md` 워크플로를 자동으로 실행합니다. 또한 사용할 수 있습니다. **실행 워크플로우** 소스 README를 편집하지 않고 번역본을 재생성하고 싶을 때마다 다시 한 번 사용하세요.

설정하지 마세요. `from` 콘텐츠 기반 언어 검출을 원할 때 설정 `from: en` 아니면. `from: ko` 의도적으로 검출을 무시하고 언어 소스를 강제합니다. 마크다운 코드 블록, URL, HTML 및 기타 비언어 콘텐츠는 한글 및 영어 문자를 계산하기 전에 가능한 한 배제됩니다.

첫 번째 실행은 번역 모델을 다운로드합니다. 이후 실행은 액션에서 관리하는 Hugging Face 모델 캐시를 재사용합니다.

> 분기 보호를 갖는 리포지토리들은 직접 푸시들을 거부할 수 있다. `GITHUB_TOKEN`. GitHub 액션이 타겟 브랜치로 푸시하거나 풀 요청을 열기 위해 최종 단계를 조정할 수 있도록 한다.

## 특징

- 영어 → 한국어 및 한국어 → 영어 번역
- GitHub 액션 러너에서 로컬로 실행
- API 키 또는 유료 AI 서비스가 없습니다
- 자동 번역된 README 파일명 선택
- 맞춤형 소스 및 타겟 경로
- 최신 커밋으로부터의 자동 번역 방향 검출
- 변경된 마크다운 요소들만의 증분 번역
- 소스 요소가 변경되지 않는 동안 수동 번역 보정을 유지합니다.
- 안전한 자동 커밋, 페치, 리베이스 및 푸시
- 실행 중에 README가 변경될 때 스테일 번역 검출
- 울타리 코드, 인라인 코드, 인라인 및 참조 링크, 이미지, 배지, HTML, 강조점, YAML 전면 사항 및 이모지를 보존합니다.
- 생성된 README 파일에 영어/ 한국어 네비게이션을 추가합니다
- 실행 사이에 껴안는 얼굴 모델을 다운로드한 캐시

## 용도

### 어느 하나의 소스 언어에 대한 하나의 워크플로우

```yaml
- uses: choiwlsd/readme-translate-kr-en@v0.2.5
  with:
    source-file: README.md
```

이것은 권장 구성입니다.

- 영어. `README.md` 생성 `README.ko.md`.
- 한국어. `README.md` 생성 `README.en.md`.
- 수동으로 보정된 타겟(README)은 타겟을 유지하며 결코 소스가 되지 않는다.

### 소스 언어를 강제

원래는 가. `from` 미설정. 자동 콘텐츠 검출이 부적합한 경우에만 설정합니다.

영어에서 한국어로:

```yaml
- uses: choiwlsd/readme-translate-kr-en@v0.2.5
  with:
    from: en
    source-file: README.md
```

한국어에서 영어로:

```yaml
- uses: choiwlsd/readme-translate-kr-en@v0.2.5
  with:
    from: ko
    source-file: README.md
```

### 커스텀 파일네임

```yaml
- uses: choiwlsd/readme-translate-kr-en@v0.2.5
  with:
    from: ko
    source-file: docs/README.md
    target-file: docs/README.en.md
```

### 자동 변경된 파일 검출

고급 워크플로우의 경우, 둘 다 생략 `from` 그리고. `source-file` 최신 커밋에서 단일 변경된 표준 README를 선택하기 위해:

```yaml
- uses: choiwlsd/readme-translate-kr-en@v0.2.5
```

사용 `fetch-depth: 0` 자동 감지 및 자동 푸시에 의존할 때:

```yaml
- uses: actions/checkout@v7
  with:
    fetch-depth: 0
```

최신 커밋에서 하나 이상의 표준 README가 변경된 경우 지정 `from` 그리고. `source-file` 노골적으로.

일반적인 단일 소스 워크플로우의 경우 선호합니다. `source-file: README.md`이것은 영문 및 한국어 저장소에 걸쳐 거동을 동일하게 유지하고 수동으로 편집된 번역이 새로운 소스로서 선택되는 것을 방지한다.

## 입력

| 입력            | 요구 사항 | 디폴트                    | 디스크립션                                   |
| ---------------- | -------- | -------------------------- | --------------------------------------------- |
| `from`           | 아니야.       | 자동 검출                | 소스 언어: `en` 아니면. `ko`                 |
| `source-file`    | 아니야.       | 자동 검출 또는 `README.md` | 소스 리드미 경로                            |
| `target-file`    | 아니야.       | 자동으로 생성    | 번역된 README 경로                        |
| `python-version` | 아니야.       | `3.11`                     | 번역 엔진에 의해 사용되는 파이썬 버전 |
| `push-changes`   | 아니야.       | `true`                     | 번역된 파일을 안전하게 커밋하고 푸시합니다       |
| `commit-message` | 아니야.       | `docs: sync bilingual README` | 번역된 파일에 대한 커밋 메시지        |
| `state-file`     | 아니야.       | `.readme-translate-state.json` | 증분 번역 상태 파일         |

## 어떻게 작동하는지.

액션은 파이썬 번역 의존성을 설치하고, 캐싱된 Hugging Face 모델을 복원하고, Markdown 요소를 보호하며, 사람이 읽을 수 있는 텍스트를 번역하고, 번역된 README를 다시 확인된 저장소에 기록한다.

구성된 소스 README는 진실의 소스입니다. 액션은 마지막 소스와 기계 생성 번역을 기록합니다. `.readme-translate-state.json`. 이후 실행 시, 소스가 변경된 마크다운 요소만을 번역합니다. 기존 타겟 요소는 사용자가 수동으로 수정한 문구를 포함하여 소스가 변경되지 않은 경우 보존됩니다. 소스 단락, 목록 항목, 헤딩 또는 기타 요소가 변경되면 이전 타겟 요소가 폐기되고 다시 번역됩니다.

워크플로우 트리거를 제한적으로 유지합니다. `README.md`, 영어를 포함하고 있는지 한국어를 포함하고 있는지에 관계없이. 편집만 `README.en.md` 아니면. `README.ko.md` 그런 다음 역번역을 시작하지 않습니다.

상태 파일은 README 파일들과 커밋된다. 레포지토리들은 최신 버전으로부터 이전 기계 번역을 복구하기 위한 상태 파일 시도 없이 릴리스로부터 업그레이드된다. `github-actions[bot]` 커밋.

푸시하기 전에 액션은 현재 브랜치를 가져온다. 관련 없는 원격 커밋이 번역 중에 나타나면 번역을 다시 기반으로 푸시합니다. README 중 하나가 원격으로 변경된 경우 기존 결과를 건너뛰어 새로운 워크플로우 실행이 최신 콘텐츠를 번역할 수 있습니다.

커밋을 직접 관리하려면 자동 푸시를 비활성화합니다.

```yaml
- uses: choiwlsd/readme-translate-kr-en@v0.2.5
  with:
    source-file: README.md
    push-changes: false
```

각 방향에 대해 전용 NLLB 기반 모델이 사용된다.

| 방향        | 모델                        |
| ---------------- | ---------------------------- |
| 영어 → 한국어 | `NHNDQ/nllb-finetuned-en2ko` |
| 한국어 → 영어 | `NHNDQ/nllb-finetuned-ko2en` |

README 콘텐츠는 러너에서 처리되며 OpenAI, Anthropic, Gemini, DeepL 또는 다른 유료 번역 API로 전송되지 않습니다.

> 번역 모델은 별도로 배포되어 있으며 고유의 라이선스와 사용 조건을 가지고 있다. 재배포 또는 상업적 사용 전에 해당 Hugging Face 모델 카드를 검토한다.

## 지역 개발

저장소에는 개발 CLI도 포함되어 있지만 현재 npm으로 게시되지 않는다.

요건:

- 노드.js 20+
- 파이썬 3.10+

```bash
python -m venv .venv
python -m pip install -r requirements.txt
node ./src/cli.js sync --from en --source README.md
```

윈도우에서 다음과 같이 가상 환경을 활성화합니다.

```powershell
.venv\Scripts\Activate.ps1
```

테스트 스위트를 다음과 같이 실행하십시오.

```bash
npm test
```

단위 테스트는 번역 모델을 다운로드하거나 로드하지 않습니다.

## 방출

현재 마켓플레이스 릴리스는 [`v0.2.5`](https://github.com/choiwlsd/readme-translate-kr-en/releases/tag/v0.2.5). 풀 릴리스 태그를 붙이는 것은 재현 가능한 동작을 제공합니다.

```yaml
uses: choiwlsd/readme-translate-kr-en@v0.2.5
```

## 라이선스

이 프로젝트는 라이선스 하에 있습니다. [MIT License](./LICENSE).
