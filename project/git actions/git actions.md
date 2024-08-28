
### 1. GitHub Actions란?

`GitHub Actions`는 `GitHub`에서 제공하는 `CI/CD(Continuous Integration/Continuous Delivery)` 도구로, 코드 변경이 발생할 때마다 **자동으로 워크플로우를 실행**할 수 있도록 해줍니다. `GitHub Actions`를 사용하면 **테스트, 빌드, 린트, 배포 등의 작업을 자동화**하여 개발 과정을 크게 개선할 수 있습니다.

---
### 주요 개념

- **워크플로우(Workflow):** GitHub Actions의 기본 단위로, 특정 이벤트가 발생할 때 실행되는 일련의 작업(job)들을 정의합니다.
- **이벤트(Event):** 워크플로우를 트리거하는 GitHub의 활동입니다. 예를 들어, 코드 푸시, PR(Pull Request) 생성, 스케줄링된 시간 등이 이벤트가 될 수 있습니다.
- **잡(Job):** 워크플로우 내에서 실행되는 하나의 작업 단위입니다. 각 잡은 여러 개의 스텝(step)으로 구성됩니다.
- **스텝(Step):** 잡 내에서 순차적으로 실행되는 명령어나 액션(action)을 의미합니다.
- **액션(Action):** 스텝에서 실행되는 개별적인 작업으로, 예를 들어 특정 스크립트를 실행하거나, 사전 정의된 GitHub 액션을 호출하는 것 등이 포함됩니다.

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
