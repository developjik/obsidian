### PWA 란?

`PWA(Progressive Web Application)`는 웹과 네이티브 앱의 장점을 결합한 애플리케이션 형태입니다. 기존 웹 애플리케이션에 새로운 기능을 추가하여 사용자가 웹사이트를 마치 네이티브 앱처럼 사용할 수 있게 만드는 기술입니다. `PWA`는 웹 표준을 기반으로 하며, 주로 다음과 같은 기능을 포함합니다:

1. **서비스 워커(Service Worker):** 네트워크를 통하지 않고도 앱을 실행할 수 있게 해주는 백그라운드 스크립트로, 오프라인 상태에서도 기능을 사용할 수 있도록 합니다.
2. **앱 매니페스트(Web App Manifest):** 웹 애플리케이션의 아이콘, 이름, 색상, 테마 등을 정의하여 홈 화면에 추가했을 때 마치 네이티브 앱처럼 보이게 만듭니다.
3. **HTTPS:** 보안을 위해 모든 `PWA`는 `HTTPS`에서만 동작합니다.

---
### 장점

1. **오프라인 지원:**
   - `PWA`는 서비스 워커를 통해 네트워크 연결 없이도 콘텐츠를 제공할 수 있어, 사용자가 네트워크 상태에 상관없이 애플리케이션을 사용할 수 있습니다.
   
2. **설치가 필요 없음:**
   - `PWA`는 앱스토어나 플레이스토어를 거치지 않고, 사용자가 웹 브라우저에서 직접 설치할 수 있어 배포가 용이합니다.
   
3. **빠른 로딩 속도:**
   - 서비스 워커를 통해 파일을 캐싱하고 필요한 리소스를 미리 로드하여, 사용자는 빠르게 앱을 사용할 수 있습니다.
   
4. **푸시 알림:**
   - 네이티브 앱처럼 푸시 알림을 지원하여 사용자가 웹 애플리케이션에서 푸시 알림을 받을 수 있습니다.
   
5. **다양한 기기 호환성:**
   - `PWA`는 웹 기술을 기반으로 하기 때문에, 다양한 운영체제와 기기에서 동일하게 사용할 수 있습니다.

6. **SEO 최적화:**
   - `PWA`는 웹의 특성을 그대로 가지므로, 검색 엔진에 잘 노출될 수 있어 `SEO`에 유리합니다.

7. **저렴한 유지 보수:**
   - 한 번 개발하면 모든 플랫폼에서 동작할 수 있으므로, 네이티브 앱과 달리 각 플랫폼별로 앱을 따로 개발하거나 유지보수할 필요가 없습니다.

---

### 단점

1. **기능적 제한:**
   - 네이티브 앱에 비해 하드웨어 접근이나 특정 기능이 제한될 수 있습니다. 예를 들어, 블루투스, 근거리 무선 통신(NFC), 배터리 정보 등의 접근은 제한될 수 있습니다.
   
2. **브라우저 호환성:**
   - 모든 브라우저가 `PWA`의 모든 기능을 지원하지 않을 수 있습니다. 특히 iOS 사파리 브라우저에서 PWA 기능 지원이 부족할 수 있습니다(푸시 알림, 백그라운드 동작 등).
   
3. **앱 스토어 노출 제한:**
   - `PWA`는 전통적인 앱스토어에 등록되지 않으므로, 앱스토어에서 발생하는 자연 유입이 부족할 수 있습니다.
   
4. **오프라인 기능의 한계:**
   - 오프라인 캐싱 기능이 있지만, 복잡한 애플리케이션의 경우 네트워크가 필요한 작업을 완전히 대체하지 못할 수 있습니다.
   
5. **앱 퍼포먼스:**
   - `PWA`는 웹 브라우저에서 실행되므로, 복잡한 그래픽 작업이나 고성능이 요구되는 애플리케이션에서는 네이티브 앱보다 성능이 떨어질 수 있습니다.

---
### NextJS Page Router 기반 PWA 설정

#### 설치 및 설정

1. 필요한 패키지 설치:
```bash
npm install next-pwa
```

2. `next.config.js` 파일 수정:

```javascript
const withPWA = require('next-pwa')({
  dest: 'public',
  disable: process.env.NODE_ENV === 'development'
})

module.exports = withPWA({
  // 다른 Next.js 설정들...
})
```

3. `public` 폴더에 `manifest.json` 파일 생성:

```json
{
  "name": "My NextJS PWA",
  "short_name": "NextPWA",
  "description": "A PWA built with NextJS",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#000000",
  "icons": [
    {
      "src": "/icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
```

4. `pages/_document.js` 파일에 메타 태그 추가:

```javascript
import { Html, Head, Main, NextScript } from 'next/document'

export default function Document() {
  return (
    <Html>
      <Head>
        <link rel="manifest" href="/manifest.json" />
        <meta name="theme-color" content="#000000" />
      </Head>
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```

#### PWA 오프라인 기능 구현

`NextJS` `PWA`에서 오프라인 기능을 구현하기 위해 서비스 워커를 사용합니다. `next-pwa`가 자동으로 서비스 워커를 생성하지만, 추가적인 오프라인 전략을 구현할 수 있습니다.

1. `next.config.js`에 오프라인 전략 추가:

```javascript
const withPWA = require('next-pwa')({
  dest: 'public',
  disable: process.env.NODE_ENV === 'development',
  runtimeCaching: [
    {
      urlPattern: /^https?.*/,
      handler: 'NetworkFirst',
      options: {
        cacheName: 'offlineCache',
        expiration: {
          maxEntries: 200,
        },
      },
    },
  ],
})

module.exports = withPWA({
  // 다른 Next.js 설정들...
})
```

2. 오프라인 상태 감지 컴포넌트 생성:

```jsx
import { useState, useEffect } from 'react'

export default function OfflineDetector() {
  const [isOnline, setIsOnline] = useState(true)

  useEffect(() => {
    setIsOnline(navigator.onLine)
    window.addEventListener('online', () => setIsOnline(true))
    window.addEventListener('offline', () => setIsOnline(false))

    return () => {
      window.removeEventListener('online', () => setIsOnline(true))
      window.removeEventListener('offline', () => setIsOnline(false))
    }
  }, [])

  if (isOnline) return null

  return (
    <div className="fixed bottom-0 left-0 right-0 bg-yellow-500 text-white p-2 text-center">
      현재 오프라인 상태입니다. 일부 기능이 제한될 수 있습니다.
    </div>
  )
}
```

#### PWA 푸시 알림 구현

푸시 알림을 구현하기 위해 `Firebase Cloud Messaging (FCM)`을 사용합니다.

1. Firebase 프로젝트 설정 및 필요한 패키지 설치:

```bash
npm install firebase
```

2. Firebase 초기화 (`firebase.js`):

```javascript
import { initializeApp } from 'firebase/app';
import { getMessaging, getToken, onMessage } from "firebase/messaging";

const firebaseConfig = {
  // Firebase 콘솔에서 가져온 설정
};

const app = initializeApp(firebaseConfig);
const messaging = getMessaging(app);

export { messaging };
```

3. 푸시 알림 구독 컴포넌트 생성:

```jsx
import { useState, useEffect } from 'react';
import { getToken } from "firebase/messaging";
import { messaging } from '../firebase';

export default function PushNotificationSubscriber() {
  const [token, setToken] = useState(null);

  useEffect(() => {
    const requestPermission = async () => {
      try {
        await Notification.requestPermission();
        const token = await getToken(messaging, { vapidKey: 'YOUR_VAPID_KEY' });
        setToken(token);
        // 여기서 토큰을 서버로 보내 저장해야 합니다
      } catch (error) {
        console.error('알림 권한 요청 실패:', error);
      }
    };

    requestPermission();
  }, []);

  return (
    <div>
      {token ? (
        <p>푸시 알림이 활성화되었습니다.</p>
      ) : (
        <p>푸시 알림을 활성화하려면 권한을 허용해주세요.</p>
      )}
    </div>
  );
}
```

4. 서비스 워커 파일 생성 (`public/firebase-messaging-sw.js`):

```javascript
importScripts('https://www.gstatic.com/firebasejs/9.0.0/firebase-app-compat.js');
importScripts('https://www.gstatic.com/firebasejs/9.0.0/firebase-messaging-compat.js');

firebase.initializeApp({
  // Firebase 설정
});

const messaging = firebase.messaging();

messaging.onBackgroundMessage(function(payload) {
  console.log('백그라운드 메시지 수신:', payload);
  
  const notificationTitle = payload.notification.title;
  const notificationOptions = {
    body: payload.notification.body,
    icon: '/icon-192x192.png'
  };

  self.registration.showNotification(notificationTitle, notificationOptions);
});
```

## 4. PWA 설치 프롬프트

PWA 설치 프롬프트를 구현하여 사용자가 쉽게 앱을 설치할 수 있도록 합니다.

```jsx
import { useState, useEffect } from 'react';

export default function PWAInstallPrompt() {
  const [deferredPrompt, setDeferredPrompt] = useState(null);

  useEffect(() => {
    window.addEventListener('beforeinstallprompt', (e) => {
      e.preventDefault();
      setDeferredPrompt(e);
    });
  }, []);

  const handleInstallClick = () => {
    if (deferredPrompt) {
      deferredPrompt.prompt();
      deferredPrompt.userChoice.then((choiceResult) => {
        if (choiceResult.outcome === 'accepted') {
          console.log('사용자가 PWA 설치를 수락했습니다.');
        }
        setDeferredPrompt(null);
      });
    }
  };

  if (!deferredPrompt) return null;

  return (
    <button onClick={handleInstallClick} className="bg-blue-500 text-white p-2 rounded">
      앱 설치하기
    </button>
  );
}
```

## 결론

NextJS Page Router 기반의 PWA 앱 개발은 강력한 웹 애플리케이션을 만들 수 있는 훌륭한 방법입니다. 오프라인 기능과 푸시 알림을 구현함으로써 사용자 경험을 한층 더 향상시킬 수 있습니다. 

이 가이드를 따라 구현하면 다음과 같은 기능을 갖춘 PWA를 개발할 수 있습니다:
- NextJS의 강력한 기능을 활용한 웹 애플리케이션
- 오프라인 작동 가능
- 푸시 알림 지원
- 설치 가능한 웹 앱

PWA 개발 시 주의할 점은 사용자 경험을 항상 최우선으로 고려해야 한다는 것입니다. 오프라인 기능, 푸시 알림, 설치 프롬프트 등을 적절히 활용하여 사용자에게 최상의 경험을 제공하세요.

마지막으로, PWA 기술은 계속 발전하고 있으므로, 최신 트렌드와 모범 사례를 지속적으로 학습하고 적용하는 것이 좋습니다. NextJS와 PWA의 강력한 조합을 통해 여러분의 웹 애플리케이션이 더욱 강력하고 사용자 친화적으로 발전하기를 바랍니다!