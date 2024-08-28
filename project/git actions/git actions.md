
### 1. GitHub Actions란?

`GitHub Actions`는 `GitHub`에서 제공하는 `CI/CD(Continuous Integration/Continuous Delivery)` 도구로, 코드 변경이 발생할 때마다 **자동으로 워크플로우를 실행**할 수 있도록 해줍니다. `GitHub Actions`를 사용하면 **테스트, 빌드, 린트, 배포 등의 작업을 자동화**하여 개발 과정을 크게 개선할 수 있습니다.

---
### 주요 개념

GitHub Actions의 핵심 개념들을 더 자세히 설명한 내용을 표로 정리해 보았습니다.

| 개념 | 설명 | 예시 |
|------|------|------|
| **워크플로우 (Workflow)** | GitHub Actions의 기본 단위로, 특정 이벤트가 발생할 때 실행되는 일련의 작업(job)들을 정의합니다. 워크플로우는 리포지토리의 `.github/workflows/` 디렉토리에 YAML 파일로 저장됩니다. | 코드 푸시 또는 PR 생성 시 자동으로 테스트를 실행하거나 애플리케이션을 배포하는 작업들을 하나의 워크플로우로 구성할 수 있습니다. |
| **이벤트 (Event)** | 워크플로우를 트리거하는 GitHub의 활동입니다. 워크플로우는 특정 이벤트가 발생할 때 실행됩니다. | 코드 푸시(`push`), PR 생성(`pull_request`), 특정 시간(`schedule`)에 실행되도록 설정할 수 있습니다. |
| **잡 (Job)** | 워크플로우 내에서 실행되는 하나의 작업 단위입니다. 각 잡은 여러 개의 스텝(step)으로 구성되며, 병렬로 실행될 수도 있고, 순차적으로 실행될 수도 있습니다. | 테스트 잡, 빌드 잡, 배포 잡 등으로 나누어 각 잡이 독립적으로 실행될 수 있습니다. |
| **스텝 (Step)** | 잡 내에서 순차적으로 실행되는 개별 명령어 또는 액션을 의미합니다. 스텝은 단일 명령어를 실행하거나, 외부 액션을 호출할 수 있습니다. | `npm install`로 의존성을 설치하거나, `pytest`로 테스트를 실행하는 스텝을 정의할 수 있습니다. |
| **액션 (Action)** | 스텝에서 실행되는 개별적인 작업으로, 특정 스크립트를 실행하거나 사전 정의된 GitHub 액션을 호출하는 작업입니다. GitHub Marketplace에서 제공되는 수많은 액션들을 사용할 수 있습니다. | `actions/checkout@v2`를 사용해 리포지토리의 코드를 체크아웃하거나, `actions/setup-node@v2`로 Node.js 환경을 설정하는 액션을 사용할 수 있습니다. |

1. **워크플로우 (Workflow):**
   - 여러 개의 잡을 포함할 수 있으며, 잡들 간의 의존성을 설정할 수 있습니다. 예를 들어, 테스트가 성공적으로 완료되었을 때만 배포 작업을 실행하도록 워크플로우를 구성할 수 있습니다.
  
2. **이벤트 (Event):**
   - GitHub에서 제공하는 다양한 이벤트를 트리거로 설정할 수 있습니다. 예를 들어, `issue_comment` 이벤트를 사용하면 이슈에 댓글이 달릴 때마다 워크플로우를 실행할 수 있습니다.
  
3. **잡 (Job):**
   - 각 잡은 독립적인 환경에서 실행됩니다. 예를 들어, 하나의 잡은 Ubuntu 환경에서 실행되고, 다른 잡은 Windows 환경에서 실행될 수 있습니다.
  
4. **스텝 (Step):**
   - 스텝은 잡 내에서 순차적으로 실행되며, 실패할 경우 그 다음 스텝이 실행되지 않을 수 있습니다. 필요한 경우 특정 스텝이 실패해도 다음 스텝을 계속 실행하도록 설정할 수도 있습니다.
  
5. **액션 (Action):**
   - 액션은 재사용 가능한 코드 블록으로, GitHub Marketplace에서 다양한 액션을 다운로드해 사용할 수 있습니다. 예를 들어, AWS S3에 파일을 업로드하는 액션, Docker 이미지를 빌드하는 액션 등이 있습니다.

---
### 기본 구조

`GitHub Actions` 워크플로우는 `.github/workflows/` 디렉토리에 `YAML 파일`로 저장됩니다. 가장 기본적인 예제를 통해 구조를 살펴보겠습니다.

```yaml
name: CI

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '14'
    - name: Install dependencies
      run: npm install
    - name: Run tests
      run: npm test
```

이 예제는 코드 푸시(push) 이벤트가 발생할 때마다 Node.js 애플리케이션의 테스트를 실행하는 간단한 워크플로우입니다.

- **name:** 워크플로우의 이름입니다.
- **on:** 워크플로우가 실행되는 이벤트를 정의합니다. 이 예제에서는 `push` 이벤트가 트리거입니다.
- **jobs:** 워크플로우에서 실행될 작업들을 정의합니다. 각 작업은 독립적으로 실행될 수 있습니다.
- **runs-on:** 잡이 실행될 환경을 정의합니다. `ubuntu-latest`는 최신 버전의 우분투에서 실행됨을 의미합니다.
- **steps:** 잡 내에서 순차적으로 실행되는 단계들을 정의합니다.

---
###  활용 사례

1. **CI/CD 파이프라인 구축:**
   - `GitHub Actions`를 이용하여 코드가 리포지토리에 푸시될 때마다 **자동으로 빌드하고, 테스트하고, 배**포까지 할 수 있습니다.
   - 예를 들어, `master` 브랜치에 푸시되면 프로덕션 환경에 자동 배포되도록 설정할 수 있습니다.

2. **코드 품질 관리:**
   - 코드를 커밋할 때마다 린트나 코드 스타일 검사기를 자동으로 실행하여 코드 품질을 유지할 수 있습니다.
   - PR 생성 시 자동으로 코드 리뷰 툴을 실행하여 문제를 미리 발견할 수 있습니다.

3. **정기적 작업 실행:**
   - 특정 시간에 반복적으로 실행해야 하는 작업(예: 데이터베이스 백업, 리포트 생성 등)을 자동화할 수 있습니다.
   - 예를 들어, 매일 자정에 데이터 백업 스크립트를 실행하도록 설정할 수 있습니다.

---
#### 커스텀 액션 만들기

GitHub Actions는 사전 정의된 액션 외에도, 사용자가 직접 커스텀 액션을 만들어 사용할 수 있습니다. 커스텀 액션은 JavaScript로 작성하거나 Docker 컨테이너로 정의할 수 있습니다. 

**예제: JavaScript로 작성된 커스텀 액션**

```javascript
// index.js
const core = require('@actions/core');

try {
  const nameToGreet = core.getInput('who-to-greet');
  console.log(`Hello ${nameToGreet}!`);
  const time = (new Date()).toTimeString();
  core.setOutput("time", time);
} catch (error) {
  core.setFailed(error.message);
}
```

위 예제는 "Hello {name}!" 메시지를 출력하고 현재 시간을 반환하는 간단한 커스텀 액션입니다. 이를 통해 특정 프로젝트에 맞춤화된 워크플로우를 구성할 수 있습니다.
