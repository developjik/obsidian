### **📌 OpenAPI 기반 TypeScript 도구 비교**

|**비교 항목**|`openapi-typescript`|`OpenAPI Generator`|`Swagger Codegen`|`openapi-zod-client`|
|---|---|---|---|---|
|**타입 자동 생성**|✅ TS 타입|✅ TS 타입 + API 클라이언트|✅ TS 타입 + API 클라이언트|✅ TS 타입 + Zod 스키마|
|**Zod 스키마 지원**|❌ 미지원|❌ 미지원|❌ 미지원|✅ 내장|
|**런타임 검증**|❌ 없음|❌ 없음|❌ 없음|✅ Zod 기반 검증|
|**API 클라이언트 생성**|❌ 없음|✅ Axios/Fetch/Node 지원|✅ Axios/Fetch/Node 지원|✅ Fetch 기반 클라이언트|
|**검증 커스터마이징**|❌ 없음|❌ 제한적|❌ 제한적|✅ Zod 확장 가능|
|**의존성**|없음|`@openapitools/openapi-generator-cli`|`swagger-codegen`|`Zod`, `openapi-zod-client`|

---

### 🎯 **결론**

- **타입만 필요하고 가볍게 사용한다면** → `openapi-typescript`
- **타입 + API 클라이언트 코드가 필요하다면** →`OpenAPI Generator` 또는 `Swagger Codegen`
- **타입 + API 클라이언트 + Zod Schema + 런타임 검증이 필요하다면** → `openapi-zod-client`