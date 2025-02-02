
### 1. **환경 변수 파일 사용**
   - `Next.js`는 `.env` 파일을 통해 환경 변수를 설정할 수 있으며, 이를 `process.env`로 접근 가능합니다.
   - 이러한 환경 변수는 서버 측 코드에서만 사용되며, 클라이언트 측에서는 접근할 수 없습니다.
   - 예시로, 데이터베이스 연결 정보를 `.env` 파일에 넣으면, `Next.js`에서 자동으로 `Node.js` 환경에서 해당 변수를 로드하여 사용할 수 있습니다.

   **예시:**
   `.env` 파일:
   ```env
   DB_HOST=localhost
   DB_USER=myuser
   DB_PASS=mypassword
   ```

   **사용 예시 (데이터베이스 연결):**
   ```javascript
   // pages/index.js
   export async function getStaticProps() {
     const db = await myDB.connect({
       host: process.env.DB_HOST,
       username: process.env.DB_USER,
       password: process.env.DB_PASS,
     })
     // ...
     return { props: {} }
   }
   ```

   여기서 `process.env.DB_HOST`, `process.env.DB_USER`, `process.env.DB_PASS`가 `Node.js` 환경에서 불러와지며, 데이터베이스 연결에 사용됩니다.
   
---
### 2. **@next/env 패키지를 사용한 환경 변수 로드**
   - Next.js 외부 환경에서도 환경 변수를 로드해야 하는 경우, `@next/env` 패키지를 사용하여 환경 변수를 불러올 수 있습니다.
   - 이 패키지를 설치한 후 `loadEnvConfig` 함수를 사용하여 환경 변수를 로드할 수 있습니다.
   - 예를 들어 **ORM 설정**이나 **테스트 환경**에서는 환경 변수를 불러올 수 있습니다.

   **설치 및 사용 예시:**
   ```bash
   npm install @next/env
   ```

   ```typescript
   // envConfig.ts
   import { loadEnvConfig } from '@next/env'
   
   const projectDir = process.cwd() // 프로젝트 루트 디렉토리
   loadEnvConfig(projectDir) // .env 파일 로드
   ```

   **ORM 설정에서 사용 예시:**
   ```typescript
   // orm.config.ts
   import './envConfig.ts'
   
   export default defineConfig({
     dbCredentials: {
       connectionString: process.env.DATABASE_URL!,
     },
   })
   ```

---
### 3. **다른 변수 참조하기**
   - `.env` 파일 내에서 `$` 기호를 사용하여 다른 변수를 참조할 수 있습니다. 예를 들어, `TWITTER_URL=https://x.com/$TWITTER_USER`처럼 사용할 수 있습니다.

   **예시:**
   ```env
   TWITTER_USER=nextjs
   TWITTER_URL=https://x.com/$TWITTER_USER
   ```

   **해석:**
   `process.env.TWITTER_URL`는 `"https://x.com/nextjs"`가 됩니다. 다른 변수를 참조하여 값을 설정할 때 유용합니다.

   **주의사항:** 실제 값에 `$` 기호가 포함되어야 한다면, `\$`처럼 이스케이프 문자를 사용해야 합니다.
   
---

### 4. **브라우저용 환경 변수 (NEXT_PUBLIC_ 접두사)**
   - 기본적으로 `NEXT_PUBLIC_`으로 시작하는 환경 변수만 브라우저에서 사용할 수 있습니다. 이러한 변수들은 빌드 타임에 자바스크립트 번들로 인라인됩니다.
   - 예를 들어, `NEXT_PUBLIC_ANALYTICS_ID` 환경 변수를 정의하면, 브라우저 코드에서 이를 사용할 수 있습니다.

   **예시:**
   ```env
   NEXT_PUBLIC_ANALYTICS_ID=abcdefghijk
   ```

   **사용 예시 (브라우저 코드에서):**
   ```javascript
   // pages/index.js
   import setupAnalyticsService from '../lib/my-analytics-service'
   
   setupAnalyticsService(process.env.NEXT_PUBLIC_ANALYTICS_ID)

   function HomePage() {
     return <h1>Hello World</h1>
   }
   
   export default HomePage
   ```

   여기서 `process.env.NEXT_PUBLIC_ANALYTICS_ID`는 빌드 시점에 자바스크립트 코드에 인라인됩니다. 즉, **클라이언트 측에서도 사용**될 수 있게 변환됩니다.

   **중요 사항:**  
   - 빌드 이후에는 해당 환경 변수 값이 고정됩니다. 따라서 다른 환경에서 동일한 빌드를 사용하려면 빌드 시 적절한 값을 설정해야 합니다.
   - **동적으로 변수를 참조**하려고 하면 이 값은 인라인되지 않습니다. 예를 들어 `const varName = 'NEXT_PUBLIC_ANALYTICS_ID'; process.env[varName]`와 같은 방식은 지원되지 않습니다.

---
### 5. **런타임 환경 변수**
   - Next.js는 빌드 타임 및 런타임 환경 변수를 지원합니다. 서버에서 런타임 환경 변수를 사용하려면 `getServerSideProps` 또는 App Router를 사용하여 환경 변수를 안전하게 서버에서 읽을 수 있습니다.

   **예시:**
   ```javascript
   import { unstable_noStore as noStore } from 'next/cache'
   
   export default function Component() {
     noStore() // 이 함수는 동적 렌더링을 활성화합니다.
     const value = process.env.MY_VALUE
     return <div>{value}</div>
   }
   ```

   이 경우, 환경 변수 `MY_VALUE`는 **런타임**에 서버 측에서 읽히며 클라이언트로 전달되지 않습니다.

---
### 6. **기본 환경 변수 파일 (.env, .env.development, .env.production)**
   - 기본적으로 모든 환경에서 `.env` 파일이 로드됩니다. 개발 환경에서는 `.env.development`, 프로덕션 환경에서는 `.env.production` 파일을 사용하여 환경 변수를 설정할 수 있습니다.

   **예시:**
   ```env
   // .env.development
   API_URL=http://localhost:3000

   // .env.production
   API_URL=https://api.myapp.com
   ```

   개발 중에는 `http://localhost:3000`, 프로덕션에서는 `https://api.myapp.com`이 각각 사용됩니다.

---
### 7. **Vercel에서의 환경 변수 설정**
   - Vercel에 배포할 때 환경 변수는 프로젝트 설정에서 구성할 수 있으며, 이를 로컬 환경으로 다운로드하여 사용할 수 있습니다.
---
### 8. **테스트 환경 변수**
   - 테스트 환경에서는 `.env.test` 파일을 사용할 수 있습니다. 하지만 `.env.local` 파일은 테스트 환경에서는 로드되지 않도록 설정됩니다.

   테스트 환경에서는 `.env.test` 파일을 사용할 수 있습니다. 이는 Jest나 Cypress 같은 테스트 도구에서만 사용됩니다.

   **예시:**
   ```env
   // .env.test
   API_URL=http://localhost:4000
   ```

   테스트 시에는 `.env.test` 파일에서 변수를 읽어오며, `.env.local` 파일은 로드되지 않습니다. 이는 테스트의 일관성을 유지하기 위한 것입니다.
   
---
### 9. **환경 변수 로드 순서**
   - 환경 변수는 특정 순서로 로드됩니다. 먼저 `process.env`에서 확인한 후, `.env.local`, `.env.[NODE_ENV]` 파일의 순서로 변수를 찾습니다.
   - `Next.js`는 환경 변수를 특정 순서대로 로드합니다. 다음 중 첫 번째로 찾은 값을 적용합니다.
   1. `process.env`
   2. `.env.$(NODE_ENV).local`
   3. `.env.local`
   4. `.env.$(NODE_ENV)`
   5. `.env`

   **예시:**
   만약 `NODE_ENV`가 `development`로 설정되어 있고, `.env.development.local`과 `.env`에 동일한 변수가 정의되어 있으면 `.env.development.local`의 값이 우선됩니다.

   ```env
   // .env.development.local
   API_URL=http://localhost:5000

   // .env
   API_URL=http://localhost:3000
   ```

   이 경우, `process.env.API_URL`은 `http://localhost:5000`으로 설정됩니다.
