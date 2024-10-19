### JWT란?

`JWT`는 `JSON Web Token`의 약자로, 당사자 간에 정보를 안전하게 전송하기 위한 개방형 표준(RFC 7519)입니다. 이 정보는 디지털 서명이 되어 있어 신뢰할 수 있습니다.

---
### 구조

`JWT`는 세 부분으로 구성되어 있습니다:

1. **헤더 (Header)**
2. **페이로드 (Payload)**
3. **서명 (Signature)**

각 부분은 점(.)으로 구분되며, Base64Url 인코딩되어 있습니다.

1. 헤더 (Header)

헤더는 일반적으로 토큰의 타입(`JWT`)과 사용된 `해싱 알고리즘`(예: HMAC SHA256 or RSA)을 지정합니다.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

2. 페이로드 (Payload)

페이로드에는 클레임(claim)이라 불리는 엔티티(주로 사용자)와 추가 데이터에 대한 설명이 포함됩니다.

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

3. 서명 (Signature)

서명은 헤더의 인코딩 값과 페이로드의 인코딩 값을 합친 후, 비밀키로 해싱하여 생성됩니다.

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
```

---
### 인증 과정

1. 사용자가 로그인합니다.
2. 서버는 사용자 정보를 확인하고, `JWT`를 생성하여 클라이언트에게 전송합니다.
3. 클라이언트는 이후의 요청에 `JWT`를 포함시켜 서버에 전송합니다.
4. 서버는 `JWT`를 검증하고, 요청을 처리합니다.
---
### Auth Token과 Refresh Token

`JWT`를 사용한 인증 시스템에서는 주로 두 가지 토큰을 사용합니다: `Auth Token(접근 토큰)`과 `Refresh Token(갱신 토큰)`

1. Auth Token (접근 토큰)

- 짧은 유효 기간을 가집니다 (보통 15분에서 1시간).
- 사용자의 인증 상태를 나타내며, API 요청 시 함께 전송됩니다.
- 민감한 작업을 수행할 때 사용됩니다.

2. Refresh Token (갱신 토큰)

- 긴 유효 기간을 가집니다 (보통 몇 주에서 몇 개월).
- Auth Token이 만료되었을 때 새로운 Auth Token을 발급받는 데 사용됩니다.
- 보안상 더 중요하므로, 안전하게 저장되어야 합니다.

---
### 프론트엔드에서의 토큰 처리

1. **토큰 저장**
   - `Auth Token`: 메모리 또는 브라우저의 sessionStorage에 저장
   - `Refresh Token`: HttpOnly 쿠키에 저장 (JavaScript로 접근 불가능)

   ```javascript
   // Auth Token 저장
   sessionStorage.setItem('authToken', 'your_auth_token_here');
   
   // Refresh Token은 서버에서 HttpOnly 쿠키로 설정
   ```

2. **API 요청 시 Auth Token 사용**
   
   ```javascript
   axios.get('https://api.example.com/data', {
     headers: {
       'Authorization': `Bearer ${sessionStorage.getItem('authToken')}`
     }
   });
   ```

3. **Auth Token 만료 처리**
   
   ```javascript
   axios.interceptors.response.use(
     (response) => response,
     async (error) => {
       const originalRequest = error.config;
       if (error.response.status === 401 && !originalRequest._retry) {
         originalRequest._retry = true;
         const newAuthToken = await refreshAuthToken();
         sessionStorage.setItem('authToken', newAuthToken);
         originalRequest.headers['Authorization'] = `Bearer ${newAuthToken}`;
         return axios(originalRequest);
       }
       return Promise.reject(error);
     }
   );
   
   async function refreshAuthToken() {
     // 서버에 Refresh Token을 전송하여 새 Auth Token을 받아옴
     const response = await axios.post('https://api.example.com/refresh-token');
     return response.data.authToken;
   }
   ```

4. **로그아웃 처리**
   
   ```javascript
   function logout() {
     sessionStorage.removeItem('authToken');
     // Refresh Token을 무효화하기 위해 서버에 요청
     axios.post('https://api.example.com/logout');
     // 사용자를 로그인 페이지로 리디렉션
   }
   ```

---
### 장점

1. **상태를 저장하지 않음 (Stateless)**: 서버는 사용자의 상태를 저장할 필요가 없어 확장성이 좋습니다.
2. **다양한 프로그래밍 언어에서 지원**: 대부분의 언어에서 JWT를 쉽게 생성하고 검증할 수 있습니다.
3. **모바일 애플리케이션 친화적**: 모바일 환경에서도 쉽게 사용할 수 있습니다.

---
### 단점

1. **토큰 크기**: 포함된 정보가 많을수록 토큰의 크기가 커집니다.
2. **보안**: 중요한 정보는 payload에 포함시키지 않아야 합니다.
3. **토큰 무효화의 어려움**: 일단 발급된 토큰은 만료 전까지 유효하므로, 즉시 무효화하기 어렵습니다.
---
### 보안 고려사항

1. **HTTPS 사용**: 모든 통신은 반드시 HTTPS를 통해 이루어져야 합니다.
2. **XSS 방지**: `Auth Token`을 `JavaScrip`t로 접근 가능한 곳에 저장하므로, XSS 공격에 주의해야 합니다.
3. **CSRF 방지**: `Refresh Token`을 `HttpOnly 쿠키`에 저장하고, `CSRF 토큰`을 사용하여 추가 보호를 할 수 있습니다.

> `JWT`에 대해 더 자세히 알고 싶다면, [공식 JWT 웹사이트](https://jwt.io)를 참고해보세요.

