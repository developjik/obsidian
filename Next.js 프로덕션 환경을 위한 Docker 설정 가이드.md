`프로덕션` 환경에서 `Next.js` 애플리케이션을 `Docker`로 구축하고 배포하는 방법을 상세히 알아보겠습니다. 이 가이드에서는 최적화된 이미지 크기, 보안, 그리고 성능을 고려한 멀티 스테이지 빌드 방식을 다룹니다.

## 목차
1. [프로덕션 환경 설정의 특징](#1-프로덕션-환경-설정의-특징)
2. [필요한 파일 구성](#2-필요한-파일-구성)
3. [Dockerfile 작성](#3-dockerfile-작성)
4. [Docker Compose 설정](#4-docker-compose-설정)
5. [배포 및 실행 방법](#5-배포-및-실행-방법)
6. [보안 고려사항](#6-보안-고려사항)
7. [성능 최적화 팁](#7-성능-최적화-팁)

## 1. 프로덕션 환경 설정의 특징

`프로덕션` 환경의` Docker` 설정은 개발 환경과 다음과 같은 차이점이 있습니다:

- **최소한의 이미지 크기**: 필요한 파일만 포함
- **멀티 스테이지 빌드**: 빌드 환경과 실행 환경을 분리
- **보안 강화**: 비루트 사용자 실행, 민감한 정보 보호
- **성능 최적화**: 캐시 레이어 최적화, 불필요한 의존성 제거
- **안정성**: 자동 재시작 설정, 헬스 체크 구현

## 2. 필요한 파일 구성

`Next.js`  `Docker` `프로덕션` 배포를 위해 다음 파일들이 필요합니다:

```
your-nextjs-project/
├── Dockerfile            # 프로덕션용 Dockerfile
├── docker-compose.yml    # 프로덕션 환경 설정
├── .dockerignore        # 불필요한 파일 제외 설정
├── next.config.js       # Next.js 프로덕션 설정
└── .env.production      # 프로덕션 환경변수
```

## 3. Dockerfile 작성

`프로덕션`용 `Dockerfile`은 **멀티 스테이지 빌드**를 사용하여 **최적화**합니다:

```dockerfile
# 1. 의존성 설치 단계
FROM node:18-alpine AS deps
WORKDIR /app

# 빌드에 필요한 패키지 설치
COPY package*.json ./
RUN npm ci --only=production

# 2. 빌드 단계
FROM node:18-alpine AS builder
WORKDIR /app

# 의존성 복사 및 소스 빌드
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# 3. 실행 단계
FROM node:18-alpine AS runner
WORKDIR /app

# 프로덕션 환경 설정
ENV NODE_ENV production
ENV NEXT_TELEMETRY_DISABLED 1

# 보안을 위한 비루트 사용자 생성
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

# 필요한 파일만 복사
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

# 권한 설정
RUN chown -R nextjs:nodejs /app

# 비루트 사용자로 전환
USER nextjs

# 상태 체크를 위한 헬스체크 설정
HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:3000/api/health || exit 1

EXPOSE 3000

# 실행 명령
CMD ["node", "server.js"]
```

## 4. Docker Compose 설정

프로덕션 환경을 위한 docker-compose.yml:

```yaml
version: '3.8'

services:
  nextjs:
    container_name: nextjs-production
    build:
      context: .
      dockerfile: Dockerfile
      args:
        - NEXT_PUBLIC_API_URL=${NEXT_PUBLIC_API_URL}
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - NEXT_PUBLIC_API_URL=${NEXT_PUBLIC_API_URL}
    restart: always
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/api/health"]
      interval: 30s
      timeout: 30s
      retries: 3
      start_period: 5s
    networks:
      - app-network
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 500M
        reservations:
          cpus: '0.25'
          memory: 200M

networks:
  app-network:
    driver: bridge
```

## 5. 배포 및 실행 방법

프로덕션 환경에 배포하는 단계별 방법:

```bash
# 1. 환경 변수 설정
cp .env.example .env.production
vi .env.production

# 2. 이미지 빌드
docker-compose build

# 3. 컨테이너 실행
docker-compose up -d

# 4. 로그 확인
docker-compose logs -f

# 5. 상태 확인
docker-compose ps
```

## 6. 보안 고려사항

프로덕션 환경에서 중요한 보안 설정들:

1. **비루트 사용자 실행**
   - 컨테이너는 반드시 비루트 사용자로 실행
   - 필요한 권한만 부여

2. **환경 변수 관리**
   ```yaml
   # docker-compose.yml에서
   secrets:
     db_password:
       file: ./secrets/db_password.txt
   ```

3. **민감한 정보 보호**
   - `.dockerignore`에 중요 파일 포함
   - Docker Secrets 또는 환경 변수 사용
   - 빌드 시 민감한 정보가 이미지에 포함되지 않도록 주의

## 7. 성능 최적화 팁

프로덕션 성능을 최적화하기 위한 방법들:

1. **이미지 크기 최적화**
   - 멀티 스테이지 빌드 사용
   - 불필요한 파일 제외
   - Alpine 기반 이미지 사용

2. **캐시 레이어 최적화**
   ```dockerfile
   # 자주 변경되는 파일은 나중에 복사
   COPY package*.json ./
   RUN npm ci
   COPY . .
   ```

3. **리소스 제한 설정**
   - CPU 및 메모리 제한 설정
   - 스왑 메모리 설정

4. **로깅 최적화**
   ```yaml
   logging:
     driver: "json-file"
     options:
       max-size: "10m"
       max-file: "3"
   ```

## 마무리

이렇게 설정된 Docker 환경은 프로덕션에서 안정적이고 보안이 강화된 Next.js 애플리케이션 운영이 가능합니다. 특히 멀티 스테이지 빌드를 통한 이미지 최적화와 보안 설정은 필수적인 요소입니다.

실제 운영 환경에 배포할 때는 반드시 다음 사항을 확인하세요:
- 모든 환경 변수가 올바르게 설정되었는지 확인
- 보안 설정이 제대로 되어있는지 검토
- 리소스 제한이 애플리케이션 요구사항에 맞게 설정되었는지 확인
- 로깅과 모니터링이 제대로 구성되었는지 확인

이러한 설정들을 통해 안정적이고 보안이 강화된 Next.js 애플리케이션을 운영할 수 있습니다.