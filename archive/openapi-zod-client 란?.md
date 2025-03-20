# openapi-zod-client 란?

OpenAPI (Swagger) 문서를 기반으로 **Zod 스키마 및 TypeScript API 클라이언트 코드**를 자동 생성하는 라이브러리입니다.

## **🔹 주요 기능**

**1️⃣ OpenAPI 스펙에서 Zod 스키마와 API 엔드포인트 생성**

**2️⃣ Zodios 기반 TypeScript HTTP 클라이언트 생성** (Zod 활용)

3️⃣ **에러 핸들링, 성공 응답 처리, 엄격한 객체 검증 옵션 제공**

4️⃣ **Prettier 및 핸들바 템플릿 지원** (출력 형식 맞춤 가능)

5️⃣ **Swagger/OpenAPI URL 직접 사용 가능**

6️⃣ **CLI 및 프로그래밍 방식 지원**

7️⃣ **자동화 및 CI/CD 연동 가능**

## **📌 openapi-zod-client의 장점**

✅ **자동화**: OpenAPI 문서만 있으면 Zod + Type 자동으로 생성

✅ **타입 안정성**: Zod + TypeScript 로 강력한 타입 검증 제공

✅ **런타임 검증**: API 응답을 Zod로 검증하여 예기치 않은 데이터 구조 문제 방지

✅ **쉽고 간편한 사용**: 한 번 설정하면 지속적으로 API 타입을 최신 상태로 유지 가능

## ⚠️ **주의사항**

**❌ 정확한 OpenAPI 스펙 필요**: 잘못된 스펙이 입력되면 생성된 코드도 오류를 포함할 수 있습니다.

**❌ OpenAPI 기능 지원 범위**: 일부 복잡한 OpenAPI 기능(예: `oneOf`, 커스텀 포맷)은 제한될 수 있습니다.

### 🔄 **유사 도구와 차별점**

- **Swagger Codegen**: 다양한 언어를 지원하지만 Zod와의 통합은 없습니다.
- **openapi-typescript**: 타입만 생성하며, Zod 검증은 제공하지 않습니다.

<aside>

📌

**openapi-zod-client vs openapi-typescript**

| **비교 항목** | openapi-zod-client | openapi-typescript |

| --- | --- | --- |

| 타입 자동 생성 | ✅ OpenAPI 기반 TS 타입 자동 생성 | ✅ OpenAPI 기반 TS 타입 자동 생성 |

| Zod 스키마 지원 | ✅ 지원 | ❌ 미지원 |

| 런타임 검증 | ✅ Zod로 응답 데이터 검증 가능 | ❌ 없음 |

| API 클라이언트 자동 생성 | ✅ 요청 함수 생성 (fetch 기반) | ❌ 타입만 생성 |

</aside>

## **🔹 사용법**

```warp-runnable-command
npm i -D openapi-zod-client
npm openapi-zod-client "./input/file.json" -o "./output/client.ts"
```

또는

```warp-runnable-command
npx openapi-zod-client "./input/file.yaml" -o "./output/client.ts"
```

## **🔹 주요 옵션**

| **옵션** | **설명** |

| --- | --- |

| -o, --output | 출력 파일 경로 |

| --export-schemas | 모든 #/components/schemas 내보내기 |

| --strict-objects | 객체의 추가 필드를 허용하지 않음 (default: false) |

| --prettier <path> | Prettier 설정 파일 경로 |

| --with-docs | JSDoc 주석 포함 |

## **🔹 예제**

- **OpenAPI 입력**

```warp-runnable-command
openapi: "3.0.0"
info:
    version: 1.0.0
    title: Swagger Petstore
paths:
    /pets:
        get:
            summary: List all pets
            operationId: listPets
            responses:
                "200":
                    content:
                        application/json:
                            schema:
                                $ref: "#/components/schemas/Pets"
components:
    schemas:
        Pet:
            type: object
            properties:
                id:
                    type: integer
                name:
                    type: string
        Pets:
            type: array
            items:
                $ref: "#/components/schemas/Pet"
```

- **생성된 TypeScript 코드**

```warp-runnable-command
import { makeApi, Zodios } from "@zodios/core";
import { z } from "zod";
const Pet = z.object({ id: z.number(), name: z.string() });
const Pets = z.array(Pet);
export const schemas = { Pet, Pets };
const endpoints = makeApi([
  {
    method: "get",
    path: "/pets",
    response: Pets,
  },
]);
export const api = new Zodios("http://example.com", endpoints);
```

## **🔹 언제 유용할까?**

- ✅ 다른 팀/서비스의 OpenAPI 스펙을 기반으로 API 클라이언트 자동 생성이 필요할 때
- ✅ API 타입 안전성과 런타임 검증을 Zod로 보장하고 싶을 때
- ✅ CI/CD 파이프라인에서 API 클라이언트 생성을 자동화할 때
- ✅ OpenAPI 문서 변경 시, API 클라이언트 코드 동기화가 필요할 때

## **🔹 🚀 추천하는 사용 사례**

- 프론트엔드에서 OpenAPI 기반 API 클라이언트 자동 생성
- 백엔드에서도 OpenAPI 스펙을 활용한 클라이언트 사용 가능
- CI에서 최신 API 스펙을 기반으로 API 클라이언트 자동 업데이트

[GitHub - astahmer/openapi-zod-client: Generate a zodios (typescript http client with zod validation) from an OpenAPI spec (json/yaml)](https://github.com/astahmer/openapi-zod-client?tab=readme-ov-file)

---