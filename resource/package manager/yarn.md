---
sticker: emoji//1f93d-200d-2642-fe0f
---
## 목차
1. [Yarn 소개](#yarn-소개)
2. [Yarn 설치와 기본 설정](#yarn-설치와-기본-설정)
3. [package.json과 yarn.lock](#packagejson과-yarnlock)
4. [의존성 관리](#의존성-관리)
5. [Yarn 워크스페이스](#yarn-워크스페이스)
6. [성능과 보안](#성능과-보안)
7. [실무 팁과 트러블슈팅](#실무-팁과-트러블슈팅)

## Yarn 소개

### Yarn이란?
`Yarn(Yet Another Resource Negotiator)`은 Facebook이 개발한 `JavaScript` 패키지 관리자입니다. `NPM`의 한계를 극복하기 위해 만들어졌으며, 다음과 같은 특징을 가집니다:

- 더 빠른 패키지 설치 속도
- 결정적 의존성 해결
- 향상된 보안성
- 더 나은 오프라인 지원
- 워크스페이스 기능 내장

### Yarn vs NPM
- **설치 속도**: `Yarn`은 병렬 설치와 캐시를 효율적으로 활용
- **잠금 파일**: `yarn.lock`이 `package-lock.json`보다 더 안정적
- **명령어**: 더 직관적이고 간단한 CLI 명령어 제공
- **워크스페이스**: 모노레포 지원이 더 강력

## Yarn 설치와 기본 설정

### 전역 설치
```bash
# NPM을 통한 Yarn 설치
npm install -g yarn

# 버전 확인
yarn --version

# Yarn 업데이트
yarn set version latest
```

### 기본 설정
```bash
# 기본 설정 확인
yarn config list

# 작성자 정보 설정
yarn config set init-author-name "Your Name"
yarn config set init-license "MIT"
```

## package.json과 yarn.lock

### package.json 예시
```json
{
  "name": "my-project",
  "version": "1.0.0",
  "scripts": {
    "start": "node index.js",
    "build": "webpack",
    "test": "jest"
  },
  "dependencies": {
    "react": "^17.0.2",
    "express": "^4.17.1"
  },
  "devDependencies": {
    "webpack": "^5.65.0",
    "jest": "^27.4.5"
  }
}
```

### yarn.lock의 중요성
- 정확한 버전 잠금으로 일관된 설치 보장
- 팀 전체의 의존성 버전 통일
- 배포 환경의 안정성 향상

## 의존성 관리

### 기본 명령어
```bash
# 패키지 추가
yarn add lodash

# 개발 의존성 추가
yarn add --dev jest

# 전역 패키지 추가
yarn global add typescript

# 특정 버전 설치
yarn add lodash@4.17.21

# 의존성 업데이트
yarn upgrade

# 단일 패키지 업데이트
yarn upgrade lodash

# 업데이트 가능 항목 확인
yarn outdated
```

### 의존성 정리
```bash
# 사용하지 않는 의존성 제거
yarn clean

# 의존성 트리 최적화
yarn dedupe
```

## Yarn 워크스페이스

### 워크스페이스 설정
```json
{
  "private": true,
  "workspaces": [
    "packages/*"
  ]
}
```

### 워크스페이스 명령어
```bash
# 특정 워크스페이스에 패키지 추가
yarn workspace <workspace-name> add <package>

# 모든 워크스페이스에서 스크립트 실행
yarn workspaces run <script>
```

## 성능과 보안

### 성능 최적화
```bash
# 오프라인 미러 설정
yarn config set yarn-offline-mirror ./npm-packages-offline-cache

# 프로덕션 의존성만 설치
yarn install --production

# 캐시 정리
yarn cache clean
```

### 보안 기능
```bash
# 보안 감사
yarn audit

# 자동 보안 수정
yarn audit fix

# 상세 보안 리포트
yarn audit --json
```

## 실무 팁과 트러블슈팅

### 일반적인 문제 해결
1. **설치 오류 해결**
   ```bash
   # node_modules 삭제
   rm -rf node_modules
   # yarn.lock 삭제
   rm yarn.lock
   # 재설치
   yarn install
   ```

2. **캐시 관련 문제**
   ```bash
   # 캐시 정리
   yarn cache clean
   
   # 오프라인 캐시 재생성
   yarn cache list
   ```

### .yarnrc.yml 설정 예시
```yaml
nodeLinker: node-modules
enableGlobalCache: true
nmMode: hardlinks-local

packageExtensions:
  react-dom@*:
    dependencies:
      react: '*'

yarnPath: .yarn/releases/yarn-3.2.0.cjs

# 프록시 설정
httpProxy: "http://proxy.company.com:8080"
httpsProxy: "http://proxy.company.com:8080"

# 레지스트리 설정
npmRegistryServer: "https://registry.npmjs.org"
unsafeHttpWhitelist:
  - "company-registry.com"

# 보안 설정
enableScripts: false
enableTelemetry: false

# CI 설정
enableProgressBars: false
```
