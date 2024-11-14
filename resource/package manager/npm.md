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
    "prebuild": "npm run clean",
    "build": "webpack --mode production",
    "postbuild": "echo 'Build complete!'",
    
    "clean": "rimraf dist",
    "validate": "npm run lint && npm run test",
    
    "deploy": "npm run build && npm run upload",
    "upload": "aws s3 sync dist/ s3://my-bucket/"
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
save-exact=true
package-lock=true
audit=true
fund=false
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

## 결론

NPM은 현대 JavaScript 개발의 근간을 이루는 핵심 도구입니다. 효율적인 패키지 관리와 의존성 처리를 통해 개발 생산성을 크게 향상시킬 수 있습니다. 이 가이드에서 다룬 내용을 기반으로 NPM을 더욱 효과적으로 활용하시기 바랍니다.

### 추가 학습 자원
- [NPM 공식 문서](https://docs.npmjs.com/)
- [Node.js 공식 웹사이트](https://nodejs.org/)
- [패키지 검색](https://www.npmjs.com/)

프로젝트의 규모와 요구사항에 따라 적절한 NPM 기능을 선택하고 활용하시기 바랍니다. 특히 보안 관련 사항은 정기적으로 점검하는 것이 중요합니다.