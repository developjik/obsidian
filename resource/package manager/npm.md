---
sticker: emoji//1f93d-200d-2642-fe0f
---
## 목차
1. [NPM 소개](#npm-소개)
2. [NPM 설치와 기본 설정](#npm-설치와-기본-설정)
3. [package.json 완벽 이해](#packagejson-완벽-이해)
4. [의존성 관리](#의존성-관리)
5. [NPM 스크립트 활용](#npm-스크립트-활용)
6. [보안과 모범 사례](#보안과-모범-사례)
7. [실무 팁과 트러블슈팅](#실무-팁과-트러블슈팅)

## NPM 소개

### NPM이란?
`NPM(Node Package Manager)`은 `JavaScript` 프로그래밍 언어를 위한 세계 최대의 소프트웨어 레지스트리입니다. 두 가지 주요 부분으로 구성됩니다:

1. **CLI(Command-line Interface)** - 패키지 설치 및 관리를 위한 커맨드라인 도구
2. **온라인 레포지토리** - `JavaScript` 패키지들의 공개 데이터베이스

### NPM의 중요성
- 코드 재사용성 촉진
- 의존성 관리 자동화
- 버전 관리 용이성
- 개발 워크플로우 표준화

## NPM 설치와 기본 설정

### Node.js와 함께 설치
```bash
# Node.js 버전 확인
node --version

# NPM 버전 확인
npm --version

# NPM 업데이트
npm install -g npm@latest
```

### 기본 설정
```bash
# NPM 초기 설정
npm config set init-author-name "Your Name"
npm config set init-license "MIT"

# 기본 설정 확인
npm config list
```

## package.json 완벽 이해

### 필수 필드 상세 설명
```json
{
  "name": "project-name",            // 프로젝트 이름 (필수)
  "version": "1.0.0",               // 버전 (필수)
  "description": "",                // 프로젝트 설명
  "main": "index.js",              // 진입점
  "scripts": {                      // 스크립트 정의
    "start": "node index.js",
    "build": "webpack --mode production",
    "dev": "webpack --mode development",
    "test": "jest"
  },
  "keywords": [],                   // 검색 키워드
  "author": "",                     // 작성자 정보
  "license": "ISC",                 // 라이선스
  "dependencies": {                 // 프로덕션 의존성
    "express": "^4.17.1"
  },
  "devDependencies": {             // 개발 의존성
    "webpack": "^5.65.0"
  }
}
```

### 의존성 버전 표기법
```json
{
  "dependencies": {
    "package1": "1.0.0",         // 정확한 버전
    "package2": "^1.0.0",        // 마이너 버전까지 업데이트 (1.x.x)
    "package3": "~1.0.0",        // 패치 버전만 업데이트 (1.0.x)
    "package4": "*",             // 최신 버전
    "package5": ">=1.0.0",       // 1.0.0 이상
    "package6": "1.0.0 - 1.5.0"  // 버전 범위 지정
  }
}
```

## 의존성 관리

### 패키지 설치 옵션
```bash
# 기본 설치
npm install lodash

# 특정 버전 설치
npm install lodash@4.17.21

# 개발 의존성으로 설치
npm install --save-dev jest

# 전역 설치
npm install -g typescript

# package.json의 모든 의존성 설치
npm install
```

### 의존성 업데이트
```bash
# 단일 패키지 업데이트
npm update lodash

# 모든 패키지 업데이트
npm update

# 업데이트 가능 항목 확인
npm outdated
```

## NPM 스크립트 활용

### 기본 스크립트
```json
{
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "build": "webpack --mode production",
    "test": "jest",
    "lint": "eslint src/**/*.js",
    "format": "prettier --write \"src/**/*.{js,jsx}\""
  }
}
```

### 고급 스크립트 활용
```json
{
  "scripts": {
    "prebuild": "npm run clean",     // build 스크립트 실행 전에 자동으로 실행
    "build": "webpack --mode production", // 메인 빌드 스크립트
    "postbuild": "echo 'Build complete!'", // build 스크립트 완료 후 자동으로 실행
    
    "clean": "rimraf dist",          // 빌드 디렉토리 정리
    "validate": "npm run lint && npm run test", // 린트와 테스트 순차 실행
    
    "deploy": "npm run build && npm run upload", // 빌드 후 업로드 순차 실행
    "upload": "aws s3 sync dist/ s3://my-bucket/" // AWS S3에 배포
  }
}
```

## 보안과 모범 사례

### 보안 검사
```bash
# 보안 취약점 검사
npm audit

# 취약점 자동 수정
npm audit fix

# 자세한 보안 리포트
npm audit --json
```

### package-lock.json
- 정확한 의존성 트리 보장
- 팀 멤버 간 동일한 의존성 버전 사용
- 빌드 재현성 확보

### .npmrc 설정
```ini
# 패키지 설치 시 정확한 버전을 사용 (^, ~ 같은 버전 범위 지정자를 사용하지 않음)
# 예: express: "4.17.1" (O) / express: "^4.17.1" (X)
save-exact=true

# package-lock.json 파일 생성 및 관리를 활성화
# 의존성 트리를 정확하게 고정하고 팀원 간 동일한 버전 사용을 보장
package-lock=true

# npm install 실행 시 자동으로 보안 취약점 검사 수행
# 취약한 패키지 발견 시 경고 메시지 표시
audit=true

# npm install 시 표시되는 펀딩 메시지를 비활성화
# 터미널 출력을 깔끔하게 유지
fund=false

# --------- 추가 유용한 설정들 ---------

# 회사 프록시 설정
# proxy=http://proxy.company.com:8080
# https-proxy=http://proxy.company.com:8080

# npm 기본 레지스트리 설정
# registry=https://registry.npmjs.org/

# 특정 스코프의 패키지를 위한 사설 레지스트리 설정
# @mycompany:registry=https://npm.mycompany.com/

# 로그 레벨 설정 (silent, error, warn, notice, http, timing, info, verbose, silly)
# loglevel=warn

# npm 캐시 저장 위치 지정
# cache=/custom/cache/directory

# 네트워크 요청 타임아웃 설정 (밀리초)
# timeout=30000

# SSL 인증서 검증 비활성화 (개발 환경에서만 사용 권장)
# strict-ssl=false

# --------- 보안 관련 설정 ---------

# 패키지 설치 전 항상 체크섬 검증
# verify-store-integrity=true

# 패키지의 설치 전/후 스크립트 실행 방지
# ignore-scripts=true

# --------- 초기화 관련 설정 ---------

# npm init 시 기본 author 설정
# init-author-name=Your Name
# init-author-email=your.email@example.com
# init-author-url=https://your-website.com
# init-license=MIT
# init-version=1.0.0

# --------- 성능 관련 설정 ---------

# 병렬 네트워크 요청 수 제한
# maxsockets=50

# 메모리 제한 설정 (바이트 단위)
# max-old-space-size=4096

# --------- CI/CD 환경 설정 ---------

# CI 환경에서 진행 표시줄 비활성화
# progress=false

# 에러 로그 자세히 표시
# loglevel=error

# --------- 배포 관련 설정 ---------

# npm publish 시 포함할 파일 지정
# files[]=dist
# files[]=README.md

# 배포 시 태그 설정
# tag=latest

# --------- 실험적 기능 설정 ---------

# 실험적 기능 활성화 (필요한 경우에만 사용)
# experiments-enabled=true

# --------- 캐시 관련 설정 ---------

# 캐시 최대 크기 설정 (GB 단위)
# cache-max=10

# 캐시 최소 시간 설정 (분 단위)
# cache-min=10

# npm install 시 오프라인 모드 사용
# offline=true
```

## 실무 팁과 트러블슈팅

### 캐시 관리
```bash
# 캐시 정리
npm cache clean --force

# 캐시 확인
npm cache verify
```

### 일반적인 문제 해결
1. **설치 오류**
   ```bash
   # node_modules 삭제
   rm -rf node_modules
   # package-lock.json 삭제
   rm package-lock.json
   # 재설치
   npm install
   ```

2. **버전 충돌**
   ```bash
   # 의존성 트리 확인
   npm list
   # 특정 패키지 의존성 확인
   npm list packagename
   ```

### NPM 워크스페이스
모노레포 관리를 위한 워크스페이스 설정:
```json
{
  "workspaces": [
    "packages/*"
  ]
}
```

## 성능 최적화

### 설치 성능 향상
```bash
# 프로덕션 의존성만 설치
npm install --production

# CI 환경에서의 설치
npm ci
```

### 패키지 최적화
```bash
# 사용하지 않는 패키지 찾기
npm prune

# 중복 패키지 확인
npm dedupe
```
