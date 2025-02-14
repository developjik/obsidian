
`GitHub Actions`를 개발할 때 가장 기본이 되는 두 패키지인 `@actions/core`와 `@actions/github`에 대해 자세히 알아보겠습니다. 이 두 패키지는 `GitHub Actions 워크플로우`를 구현할 때 필수적인 도구들을 제공합니다.

## @actions/core 패키지

### 개요
`@actions/core`는 `GitHub Actions`의 기본적인 기능들을 제공하는 핵심 패키지입니다. 이 패키지는 워크플로우의 입력값을 처리하고, 출력을 생성하며, 디버그 메시지를 로깅하는 등의 기본적인 기능들을 담당합니다.

### 주요 기능

1. **입력값 처리**
```typescript
import * as core from '@actions/core';

// 필수 입력값 가져오기
const requiredInput = core.getInput('required_input', { required: true });

// 선택적 입력값 가져오기
const optionalInput = core.getInput('optional_input');

// boolean 입력값 가져오기
const boolInput = core.getBooleanInput('boolean_input');
```

2. **출력 설정**
```typescript
// 단일 출력값 설정
core.setOutput('result', 'success');

// 여러 출력값 설정
core.setOutput('data', {
  status: 'complete',
  timestamp: new Date().toISOString()
});
```

3. **로깅 기능**
```typescript
// 다양한 로그 레벨 지원
core.debug('디버그 메시지');
core.info('정보 메시지');
core.warning('경고 메시지');
core.error('에러 메시지');
```

4. **비밀값 마스킹**
```typescript
// 민감한 정보 마스킹
core.setSecret('sensitive-data');
```

---
## @actions/github 패키지

### 개요
`@actions/github`는 GitHub API와 상호작용하기 위한 패키지입니다. `Octokit REST API 클라이언트`를 래핑하여 `GitHub Action`s 컨텍스트에 최적화된 기능들을 제공합니다.

### 주요 기능

1. **GitHub 클라이언트 초기화**
```typescript
import * as github from '@actions/github';

const token = core.getInput('github_token');
const octokit = github.getOctokit(token);
```

2. **컨텍스트 정보 접근**
```typescript
// 현재 워크플로우 컨텍스트 정보
const context = github.context;

// 레포지토리 정보
console.log(context.repo.owner);
console.log(context.repo.repo);

// 이벤트 정보
console.log(context.eventName);
console.log(context.payload);
```

3. **GitHub API 호출 예제**
```typescript
async function createIssueComment() {
  const octokit = github.getOctokit(token);
  
  await octokit.rest.issues.createComment({
    ...context.repo,
    issue_number: context.issue.number,
    body: '자동으로 생성된 코멘트입니다.'
  });
}
```

---
## 실제 사용 예제

두 패키지를 조합한 실제 사용 예제를 살펴보겠습니다:

```typescript
import * as core from '@actions/core';
import * as github from '@actions/github';

async function run() {
  try {
    // 입력값 가져오기
    const token = core.getInput('github_token', { required: true });
    const message = core.getInput('message', { required: true });

    // GitHub 클라이언트 초기화
    const octokit = github.getOctokit(token);

    // PR 정보 가져오기
    const pr = github.context.payload.pull_request;
    
    if (pr) {
      // PR에 코멘트 작성
      await octokit.rest.issues.createComment({
        ...github.context.repo,
        issue_number: pr.number,
        body: message
      });
      
      core.setOutput('result', 'Comment created successfully');
    }
  } catch (error) {
    core.setFailed(`Action failed: ${error.message}`);
  }
}

run();
```

---
## 모범 사례

1. **에러 처리**
   - 항상 try-catch 블록을 사용하여 에러를 적절히 처리
   - `core.setFailed()`를 사용하여 액션 실패를 명확히 표시

2. **입력값 검증**
   - 필수 입력값은 `required: true` 옵션 사용
   - 입력값의 유효성을 검사하고 적절한 에러 메시지 제공

3. **토큰 보안**
   - GitHub 토큰은 항상 비밀값으로 처리
   - 불필요한 토큰 노출 방지

---

### Shell Script vs JavaScript/TypeScript Actions 비교

#### Shell Script 기반 Actions

전통적으로 `GitHub Actions`는 `Shell Script`로 작성되어 왔습니다. `Shell Script`는 다음과 같은 특징이 있습니다:

```bash
#!/bin/bash

# 기본적인 Shell Script 기반 Action 예시
echo "::set-output name=result::$(date)"
echo "::add-mask::$SECRET_VALUE"
echo "::error::Something went wrong"
```

#### Shell Script의 장점
1. **간단한 작업에 적합**
   - 파일 조작, 시스템 명령어 실행 등 기본적인 작업이 간단함
   - 익숙한 Unix/Linux 명령어 사용 가능

2. **즉시 실행 가능**
   - 별도의 빌드 과정 없이 바로 실행 가능
   - 의존성 관리가 필요 없음

3. **가벼운 실행 환경**
   - 추가적인 런타임이 필요 없음
   - 시스템 리소스 사용이 적음

#### Shell Script의 한계
1. **복잡한 로직 구현의 어려움**
   - 복잡한 조건문과 에러 처리가 난해함
   - 코드 재사용이 어려움
   - 타입 안정성 부재

2. **디버깅의 어려움**
   - 에러 추적이 어려움
   - IDE 지원이 제한적

3. **플랫폼 의존성**
   - Windows와 Linux 환경에서 다르게 동작할 수 있음
   - 크로스 플랫폼 지원이 복잡함

#### JavaScript/TypeScript Actions의 장점

`@actions/core`와 `@actions/github`를 사용한 JavaScript/TypeScript Actions는 다음과 같은 강점이 있습니다:

1. **타입 안정성과 IDE 지원**
```typescript
interface ActionInputs {
  github_token: string;
  repository: string;
  branch: string;
}

// 타입 체크와 자동완성 지원
const inputs: ActionInputs = {
  github_token: core.getInput('github_token', { required: true }),
  repository: core.getInput('repository', { required: true }),
  branch: core.getInput('branch', { required: true })
};
```

2. **강력한 에러 처리**
```typescript
try {
  await someAsyncOperation();
} catch (error) {
  if (error instanceof GitHubApiError) {
    core.setFailed(`GitHub API Error: ${error.message}`);
  } else {
    core.setFailed(`Unexpected error: ${error.message}`);
  }
  // 스택 트레이스 로깅
  core.debug(error.stack);
}
```

3. **모듈화와 코드 재사용**
```typescript
// 재사용 가능한 유틸리티 함수
export class GitHubUtils {
  private octokit: ReturnType<typeof github.getOctokit>;

  constructor(token: string) {
    this.octokit = github.getOctokit(token);
  }

  async createPullRequest(params: CreatePullRequestParams) {
    // PR 생성 로직
  }

  async addLabels(params: AddLabelsParams) {
    // 라벨 추가 로직
  }
}
```

4. **비동기 작업 처리**
```typescript
async function processWorkflow() {
  const results = await Promise.all([
    checkoutCode(),
    fetchDependencies(),
    runTests()
  ]);
  
  core.setOutput('test_results', results);
}
```

5. **풍부한 생태계 활용**
- npm 패키지 활용 가능
- 커뮤니티 제작 라이브러리 사용
- 다양한 테스트 도구 사용 가능

6. **크로스 플랫폼 호환성**
```typescript
import { platform } from 'os';

if (platform() === 'win32') {
  // Windows 특화 로직
} else {
  // Unix 특화 로직
}
```

---
## 실제 프로젝트에서의 선택 가이드

### Shell Script 선택이 좋은 경우
1. 간단한 명령어 실행이 주된 작업일 때
2. 시스템 명령어를 많이 사용해야 할 때
3. 의존성이 거의 없는 간단한 워크플로우

### JavaScript/TypeScript Actions 선택이 좋은 경우
1. 복잡한 비즈니스 로직이 필요할 때
2. GitHub API를 많이 활용해야 할 때
3. 재사용 가능한 컴포넌트가 필요할 때
4. 타입 안정성이 중요할 때
5. 대규모 팀에서 협업해야 할 때

---
## 개발 workflow 예시

### 1. 초기 설정
```typescript
import * as core from '@actions/core';
import * as github from '@actions/github';
import { GitHubUtils } from './utils';

async function run() {
  try {
    // 설정 및 초기화 코드
  } catch (error) {
    core.setFailed(error.message);
  }
}
```

### 2. 패키지 구조
```
my-github-action/
├── src/
│   ├── main.ts        # 메인 엔트리 포인트
│   ├── utils.ts       # 유틸리티 함수
│   └── types.ts       # 타입 정의
├── dist/             # 컴파일된 코드
├── action.yml        # 액션 메타데이터
└── package.json
```

### 3. 빌드 및 배포
```json
{
  "scripts": {
    "build": "ncc build src/main.ts -o dist",
    "test": "jest",
    "lint": "eslint src/**/*.ts"
  }
}
```