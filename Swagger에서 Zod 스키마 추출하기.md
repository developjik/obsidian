`Swagger(OpenAPI)` `.yaml` 파일에서 `Zod` 스키마를 `TypeScript`로 추출하는 다양한 방법을 설명해드리겠습니다.

1. `openapi-zod-client` 사용하기
```bash
# 설치
npm install --save-dev openapi-zod-client

# 사용
npx openapi-zod-client ./swagger.yaml -o ./src/generated.ts
```

이 도구는 `Zod 스키마`와 `TypeScript 타입`을 모두 생성해줍니다.

2. `@asteasolutions/zod-to-openapi` 사용하기
```bash
# 설치
npm install --save-dev @asteasolutions/zod-to-openapi

# 사용 예시
import { createDocument, extendZodWithOpenApi } from '@asteasolutions/zod-to-openapi';
import { z } from 'zod';

extendZodWithOpenApi(z);
```

3. zodios 사용하기
```bash
# 설치
npm install @zodios/core @zodios/openapi zod

# 사용
import { makeApi } from '@zodios/core';
import { generateZodClientFromOpenAPI } from '@zodios/openapi';
```

상세 구현 예시:

1. `openapi-zod-client` 상세 사용법:
```typescript
// 설정 파일 생성 (openapi-zod.config.ts)
import { Config } from 'openapi-zod-client';

const config: Config = {
  input: './swagger.yaml',
  output: './src/generated/api.ts',
  options: {
    withDescription: true,
    defaultStatusBehavior: 'discriminated',
    unionDiscriminatorOption: 'discriminator'
  }
};

export default config;

// 생성된 코드 사용
import { schemas, api } from './generated/api';

// Zod 스키마 사용
const userSchema = schemas.User;
const validatedUser = userSchema.parse(data);

// API 클라이언트 사용
const response = await api.getUser({ params: { id: 1 } });
```

2. @asteasolutions/zod-to-openapi 상세 예시:
```typescript
import { extendZodWithOpenApi, OpenAPIRegistry } from '@asteasolutions/zod-to-openapi';
import { z } from 'zod';

extendZodWithOpenApi(z);
const registry = new OpenAPIRegistry();

// 스키마 정의
const UserSchema = z.object({
  id: z.number(),
  name: z.string(),
  email: z.string().email(),
}).openapi({ title: 'User' });

registry.register('User', UserSchema);

// OpenAPI 문서 생성
const document = registry.createDocument({
  openapi: '3.0.0',
  info: {
    title: 'API',
    version: '1.0.0',
  },
});
```

3. zodios 상세 사용법:
```typescript
import { Zodios } from '@zodios/core';
import { generateZodClientFromOpenAPI } from '@zodios/openapi';
import { z } from 'zod';

// OpenAPI 스펙에서 API 정의 생성
const apiDefinition = await generateZodClientFromOpenAPI('./swagger.yaml');

// Zodios 클라이언트 생성
const api = new Zodios('https://api.example.com', apiDefinition);

// 타입 안전한 API 호출
const user = await api.getUser({ params: { id: 1 } });
```

CI/CD 통합:
```yaml
# package.json
{
  "scripts": {
    "generate-schemas": "openapi-zod-client ./swagger.yaml -o ./src/generated/api.ts",
    "prebuild": "npm run generate-schemas"
  }
}

# GitHub Actions workflow
name: Generate Schemas
on:
  push:
    paths:
      - 'swagger/**'
jobs:
  generate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
      - run: npm install
      - run: npm run generate-schemas
      - uses: stefanzweifel/git-auto-commit-action@v4
        with:
          commit_message: "chore: update generated schemas"
```

에러 처리 및 유효성 검증:
```typescript
// API 응답 검증
import { z } from 'zod';

const responseSchema = z.object({
  data: UserSchema,
  metadata: z.object({
    timestamp: z.string().datetime(),
    requestId: z.string()
  })
});

try {
  const response = await api.getUser({ params: { id: 1 } });
  const validated = responseSchema.parse(response);
} catch (error) {
  if (error instanceof z.ZodError) {
    console.error('Validation error:', error.errors);
  }
}
```

커스텀 Zod 스키마 확장:
```typescript
// 기본 생성된 스키마 확장
const ExtendedUserSchema = schemas.User.extend({
  customField: z.string(),
  validation: z.number().min(0).max(100)
});

// 재사용 가능한 스키마 정의
const BaseSchema = z.object({
  id: z.number(),
  createdAt: z.string().datetime(),
  updatedAt: z.string().datetime()
});

const UserSchema = BaseSchema.extend({
  name: z.string(),
  email: z.string().email()
});
```

프로젝트 구조 예시:
```
src/
├── generated/
│   ├── schemas.ts     # 생성된 Zod 스키마
│   └── api.ts        # 생성된 API 클라이언트
├── schemas/
│   ├── custom.ts     # 커스텀 스키마
│   └── extensions.ts # 스키마 확장
└── api/
    └── client.ts     # API 클라이언트 설정
```

각 도구의 장단점:

openapi-zod-client:
- 장점:
  - 간단한 설정
  - TypeScript 타입과 Zod 스키마 모두 생성
  - 활발한 유지보수
- 단점:
  - 커스터마이징 옵션이 제한적

@asteasolutions/zod-to-openapi:
- 장점:
  - 유연한 스키마 정의
  - 양방향 변환 지원
- 단점:
  - 수동 설정 필요
  - 학습 곡선이 있음

zodios:
- 장점:
  - 완전한 타입 안전성
  - API 클라이언트 포함
  - 풍부한 기능
- 단점:
  - 설정이 복잡할 수 있음
  - 번들 크기가 큼

