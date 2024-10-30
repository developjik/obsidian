`Next.js` 프로젝트를 `Docker` 환경에서 개발하는 방법을 알아보겠습니다. **개발 환경**과 **프로덕션 환경** 설정을 모두 다루며, 실시간 코드 반영(**Hot Reload**)이 가능한 개발 환경 구성 방법을 상세히 설명합니다.

## 목차
1. Docker 개발 환경 구성의 장점
2. 필요한 파일 구성
3. 개발 환경 설정
4. 프로덕션 환경 설정
5. 실행 및 테스트

## 1. Docker 개발 환경 구성의 장점

- **개발 환경 표준화**
- **팀원 간 동일한 개발 환경 보장**
- **의존성 관리 용이**
- **프로덕션 환경과 유사한 개발 환경 제공**

## 2. 필요한 파일 구성

프로젝트 구조는 다음과 같습니다:

```
your-nextjs-project/
├── app/                  # Next.js 소스 코드
├── public/              # 정적 파일
├── .env                 # 환경 변수
├── .dockerignore        # Docker 제외 파일
├── docker-compose.yml   # 프로덕션 설정
├── docker-compose.dev.yml # 개발 환경 설정
├── Dockerfile           # 프로덕션 Dockerfile
├── Dockerfile.dev       # 개발용 Dockerfile
└── next.config.js       # Next.js 설정
```

## 3. 개발 환경 설정

### 3.1 개발용 Dockerfile (Dockerfile.dev)

```dockerfile
FROM node:18-alpine

WORKDIR /app

# 개발 도구 설치
RUN apk add --no-cache git

# 패키지 파일 복사 및 의존성 설치
COPY package*.json ./
RUN npm install

# Next.js 설정 파일 복사
COPY next.config.js ./
COPY tsconfig*.json ./

EXPOSE 3000
EXPOSE 3001

CMD ["npm", "run", "dev"]
```

### 3.2 개발용 Docker Compose (docker-compose.dev.yml)

```yaml
version: '3.8'

services:
  nextjs:
    container_name: nextjs-app-dev
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
      - "3001:3001"
    environment:
      - NODE_ENV=development
      - WATCHPACK_POLLING=true
    volumes:
      - .:/app
      - /app/node_modules
      - /app/.next
    restart: always
    command: npm run dev
```

### 3.3 Next.js 설정 (next.config.js)

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  webpackDevMiddleware: config => {
    config.watchOptions = {
      poll: 1000,
      aggregateTimeout: 300,
    }
    return config
  },
}

module.exports = nextConfig
```

## 4. 프로덕션 환경 설정

### 4.1 프로덕션용 Dockerfile

```dockerfile
# 의존성 설치 단계
FROM node:18-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

# 빌드 단계
FROM node:18-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# 실행 단계
FROM node:18-alpine AS runner
WORKDIR /app
ENV NODE_ENV production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

CMD ["node", "server.js"]
```

### 4.2 프로덕션용 Docker Compose (docker-compose.yml)

```yaml
version: '3.8'

services:
  nextjs:
    container_name: nextjs-app
    build:
      context: .
      dockerfile: Dockerfile
      args:
        - NEXT_PUBLIC_API_URL=${NEXT_PUBLIC_API_URL}
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    restart: always
```

## 5. 실행 및 테스트

### 5.1 개발 환경 실행

```bash
# 개발 환경 시작
docker-compose -f docker-compose.dev.yml up -d

# 로그 확인
docker-compose -f docker-compose.dev.yml logs -f
```

### 5.2 프로덕션 환경 실행

```bash
# 프로덕션 빌드 및 시작
docker-compose up -d

# 로그 확인
docker-compose logs -f
```

### 5.3 실시간 코드 반영 확인

1. 개발 서버가 실행 중인 상태에서 소스 코드 수정
2. 브라우저에서 자동으로 변경사항 반영 확인
3. 컨테이너 로그에서 재컴파일 진행 상황 확인

## 주의사항

1. **볼륨 마운트**
   - `node_modules`와 `.next` 디렉토리는 컨테이너 내부 것을 사용
   - 소스 코드는 호스트 머신과 동기화

2. **환경 변수**
   - `.env` 파일을 통해 환경 변수 관리
   - 민감한 정보는 Docker Secrets 또는 환경 변수로 관리

3. **성능 최적화**
   - 개발 환경에서 불필요한 리빌드 방지
   - 적절한 캐싱 설정으로 빌드 시간 단축

## 결론

Docker를 사용한 Next.js 개발 환경 구성은 초기 설정에 시간이 필요하지만, 일관된 개발 환경과 프로덕션 배포의 안정성을 제공합니다. 특히 팀 프로젝트에서 환경 표준화의 이점이 큽니다.