
Progressive Web App(PWA)은 웹의 장점과 네이티브 앱의 사용자 경험을 결합한 혁신적인 웹 애플리케이션입니다. 

## 1. NextJS Page Router 기반 PWA 설정

NextJS로 PWA를 구축하기 위해 다음 단계를 따릅니다:

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

## 2. 오프라인 기능 구현

NextJS PWA에서 오프라인 기능을 구현하기 위해 서비스 워커를 사용합니다. `next-pwa`가 자동으로 서비스 워커를 생성하지만, 추가적인 오프라인 전략을 구현할 수 있습니다.

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

## 3. 푸시 알림 구현

푸시 알림을 구현하기 위해 Firebase Cloud Messaging (FCM)을 사용합니다.

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