## 전통적인 try/catch의 문제점

**1️⃣ 모호한 오류 범위**

- try 블록 내에서 발생하는 모든 오류가 catch로 잡힙니다.
    - 이로인해, 의도한 함수의 오류와 개발자의 실수(예: 오타)를 구분하기 어렵습니다.

```tsx
try {
  const userData = fetchUserData(); *// API 오류*
  console.log(userdata); *// 오타로 인한 오류*
} catch (e) {
  *// API 오류인지, 오타인지 불분명*
}
```

**2️⃣ 변수 스코프 문제**

- try/catch 내에서 선언된 변수는 블록 외부에서 접근하려면 별도로 선언해야 하며, 오류 발생 시 undefined 상태로 남을 수 있습니다.

```tsx
let userData;
try {
  userData = await fetchUserData();
} catch (e) {
  *// userData가 undefined로 남음*
}
```

**3️⃣ 가독성과 유지보수성 저하**

- 복잡한 로직에서 중첩된 try/catch는 코드를 읽기 어렵게 만듭니다.

```tsx
function fetchData() {
	  try {
    const data = getDataFromAPI(); // API 호출
    try {
      const parsedData = JSON.parse(data); // JSON 파싱
      try {
        processData(parsedData); // 데이터 가공
      } catch (error) {
        console.error("데이터 가공 중 오류 발생:", error);
      }
    } catch (error) {
      console.error("JSON 파싱 중 오류 발생:", error);
    }
  } catch (error) {
    console.error("API 호출 중 오류 발생:", error);
  }
}
```