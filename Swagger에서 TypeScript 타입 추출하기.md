## 목차
1. [소개](#소개)
2. [도구별 상세 가이드](#도구별-상세-가이드)
3. [실제 프로젝트 적용 전략](#실제-프로젝트-적용-전략)
4. [CI/CD 통합 가이드](#cicd-통합-가이드)
5. [문제 해결 및 팁](#문제-해결-및-팁)

## 소개

`Backend`가 `Swagger(OpenAPI)` 명세를 제공하는 경우, `Frontend` 개발에서 **타입 안정성을 확보**하는 것은 매우 중요합니다. 이 글에서는 `Swagger(OpenAPI)` `.yaml` 파일에서 `TypeScript` **타입을 추출**하는 다양한 방법과 각각의 장단점을 상세히 알아보겠습니다.

## 도구별 상세 가이드

### 1. openapi-typescript

가장 간단하고 직관적인 방법으로, 순수하게 타입 정의만을 생성하는 도구입니다.

#### 설치 및 기본 사용법
```bash
# 설치
npm install --save-dev openapi-typescript

# 기본 사용법
npx openapi-typescript swagger.yaml -o types.ts
```

#### 고급 설정 예시
```bash
# 원격 스키마 사용
npx openapi-typescript https://api.example.com/swagger.yaml -o types.ts

# 특정 경로만 타입 생성
npx openapi-typescript swagger.yaml -o types.ts --path /users,/products
```

#### 구성 파일 사용 (openapi-typescript.config.js)
```javascript
module.exports = {
  input: './swagger.yaml',
  output: './src/types/generated.ts',
  prettier: true,
  export: true,
  pathParams: true
};
```

### 2. swagger-typescript-api

API 클라이언트 코드와 함께 타입을 생성하는 강력한 도구입니다.

#### 설치 및 기본 설정
```bash
npm install --save-dev swagger-typescript-api
```

#### 상세 설정 예시
```bash
npx swagger-typescript-api -p swagger.yaml \
  -o ./src/api \
  -n myApi.ts \
  --axios \
  --enum-names-as-values \
  --type-prefix My \
  --modular
```

#### 커스텀 템플릿 사용
```typescript
// templates/api.ejs
import { AxiosInstance } from 'axios';

export class Api {
  constructor(private axios: AxiosInstance) {}
  
  // 생성된 메서드들이 여기에 추가됩니다
}
```

### 3. openapi-generator-cli

다양한 언어와 프레임워크를 지원하는 강력한 도구입니다.

#### 설치 및 기본 설정
```bash
npm install @openapitools/openapi-generator-cli -g
```

#### 상세 설정 예시
```bash
openapi-generator-cli generate \
  -i swagger.yaml \
  -g typescript-axios \
  -o ./generated \
  --additional-properties=supportsES6=true,npmVersion=6.9.0,typescriptThreePlus=true
```

#### Maven 플러그인 설정 예시
```xml
<plugin>
    <groupId>org.openapitools</groupId>
    <artifactId>openapi-generator-maven-plugin</artifactId>
    <version>5.3.0</version>
    <executions>
        <execution>
            <goals>
                <goal>generate</goal>
            </goals>
            <configuration>
                <inputSpec>${project.basedir}/swagger.yaml</inputSpec>
                <generatorName>typescript-axios</generatorName>
                <output>${project.build.directory}/generated-sources/typescript</output>
            </configuration>
        </execution>
    </executions>
</plugin>
```

## 실제 프로젝트 적용 전략

### 소규모 프로젝트 전략
- openapi-typescript 사용 권장
- 간단한 설정으로 빠른 개발 가능
- 프로젝트 구조 예시:
```
src/
├── types/
│   ├── generated.ts    # 생성된 타입
│   └── custom.ts      # 커스텀 타입
├── api/
│   └── client.ts      # API 클라이언트
└── ...
```

### 중대형 프로젝트 전략
- swagger-typescript-api 또는 openapi-generator-cli 권장
- 모듈화된 구조와 자동화된 프로세스 필요
- 프로젝트 구조 예시:
```
src/
├── generated/
│   ├── models/
│   │   ├── User.ts
│   │   └── Product.ts
│   ├── apis/
│   │   ├── UserApi.ts
│   │   └── ProductApi.ts
│   └── index.ts
├── api/
│   ├── client.ts
│   └── interceptors/
└── ...
```

## CI/CD 통합 가이드

### GitHub Actions 워크플로우 예시
```yaml
name: Generate API Types

on:
  push:
    paths:
      - 'swagger/**'

jobs:
  generate-types:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '16'
      
      - name: Generate Types
        run: |
          npm install
          npm run generate-types
      
      - name: Commit Changes
        uses: stefanzweifel/git-auto-commit-action@v4
        with:
          commit_message: "chore: update generated types"
```

### package.json 스크립트 설정
```json
{
  "scripts": {
    "generate-types": "openapi-typescript swagger.yaml -o src/types/generated.ts",
    "prebuild": "npm run generate-types",
    "predev": "npm run generate-types",
    "validate-types": "tsc --noEmit src/types/generated.ts"
  }
}
```

## 문제 해결 및 팁

### 일반적인 문제와 해결 방법

1. 순환 참조 문제
```typescript
// 해결 방법
type Circular = {
  self?: Omit<Circular, 'self'>;
};
```

2. 선택적 필드 처리
```typescript
// 생성된 타입
interface User {
  id: number;
  name?: string;
}

// 필수 필드로 변환
type RequiredUser = Required<User>;
```

3. 타입 확장
```typescript
// 기본 생성 타입
interface ApiResponse<T> {
  data: T;
  status: number;
}

// 커스텀 확장
interface EnhancedApiResponse<T> extends ApiResponse<T> {
  timestamp: string;
  requestId: string;
}
```

### 성능 최적화 팁

1. 타입 생성 캐싱
```bash
# 캐시 디렉토리 설정
npx openapi-typescript swagger.yaml -o types.ts --cache-dir .cache
```

2. 부분 타입 생성
```bash
# 특정 경로만 생성
npx openapi-typescript swagger.yaml -o types.ts --pick "/users/*,/products/*"
```

### 참고 자료
- [openapi-typescript 공식 문서](https://github.com/drwpow/openapi-typescript)
- [swagger-typescript-api 공식 문서](https://github.com/acacode/swagger-typescript-api)
- [OpenAPI Generator 공식 문서](https://openapi-generator.tech/)