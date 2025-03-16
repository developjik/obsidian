Handlebars는 **템플릿 엔진**으로, HTML 마크업에 데이터를 **간결하고 직관적**으로 삽입할 수 있습니다.

✅ SSR/CSR 모두 지원 ✅ 가독성 높은 문법 ✅ 확장성 풍부

---

## ✨ 핵심 기능

### 🎯 직관적 문법

`{{변수}}` 방식으로 데이터를 쉽게 바인딩할 수 있습니다.

```bash
<h1>{{title}}</h1>
<p>{{description}}</p>
```

### 🔄 논리 제어

`#if`, `#each`를 사용해 조건문과 반복문을 처리합니다.

```
{{#if isLoggedIn}}
  <p>환영합니다, {{username}}님!</p>
{{else}}
  <p>로그인이 필요합니다.</p>
{{/if}}
```

### 🧩 모듈화

`Partial`을 사용해 반복되는 UI 요소를 재사용할 수 있습니다.

```
{{> header}}
<h1>메인 콘텐츠</h1>
{{> footer}}
```

### ⚙️ 확장성

커스텀 헬퍼를 추가해 복잡한 로직을 처리할 수 있습니다.

```jsx
Handlebars.registerHelper("uppercase", function(str) {
  return str.toUpperCase();
});
```

```
<p>{{uppercase title}}</p>
```

---

## 🚀 사용 방법

### 📌 Node.js에서 사용하기

```bash
npm install handlebars\
```

```jsx
const Handlebars = require("handlebars");

const template = "<h1>{{title}}</h1>";
const compiledTemplate = Handlebars.compile(template);
const result = compiledTemplate({ title: "Hello, Handlebars!" });

console.log(result); // <h1>Hello, Handlebars!</h1>
```

### 📌 브라우저에서 사용하기

```html
<script src="<https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.7.7/handlebars.min.js>"></script>
<script>
  const template = Handlebars.compile("<h1>{{title}}</h1>");
  const html = template({ title: "Hello, Handlebars!" });
  document.body.innerHTML = html;
</script>
```

---

## 💡 전문가 팁

- **Partial**은 헤더/푸터처럼 재사용성 높은 요소에 사용하세요.
- **Helper**에서 복잡한 비즈니스 로직보다는 단순 포맷팅에 집중하세요.
- **Precompile**을 사용해 프로덕션 환경에서 성능을 향상시키세요.