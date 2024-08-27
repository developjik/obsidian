### useQueryParams 란

`useQueryParams`는 Next.js 애플리케이션에서 URL 쿼리 파라미터를 쉽게 관리할 수 있도록 도와주는 커스텀 훅입니다. 이 훅은 현재 페이지의 URL에서 쿼리 파라미터를 가져오고, 이를 객체 형태로 반환하며, 다양한 방법으로 쿼리 파라미터를 설정, 업데이트, 삭제할 수 있는 유틸리티 함수들을 제공합니다. `useQueryParams`를 사용하면 복잡한 URL 쿼리 조작을 더 직관적이고 간편하게 처리할 수 있습니다.

---
### 사용조건

- `react`
- `next.js page router`

위 두조건을 사용하고 있는 프로젝트에서 다음 `useQueryParams` hook을 활용할 수 있습니다
---
### 주요 기능

1. **쿼리 파라미터 읽기 (`queryParams`)**:
   `queryParams`는 현재 URL에 포함된 모든 쿼리 파라미터를 객체로 반환합니다. 이때, JSON 형식으로 저장된 파라미터는 자동으로 파싱되어 객체나 배열로 변환됩니다.

2. **쿼리 파라미터 설정 (`setQueryParams`)**:
   `setQueryParams`를 사용하여 URL의 쿼리 파라미터를 설정할 수 있습니다. 이 함수는 새로운 파라미터를 추가하거나 기존의 파라미터를 업데이트합니다. 또한, 히스토리를 교체(replace)하거나 새 항목을 추가(push)할지 선택할 수 있으며, 객체나 배열을 JSON 문자열로 직렬화하여 저장할 수 있습니다.

3. **쿼리 파라미터 업데이트 (`updateQueryParams`)**:
   페이지 이동 없이 쿼리 파라미터를 업데이트할 때 사용합니다. 이 함수는 shallow 라우팅을 사용하여 URL 쿼리 문자열만 변경하고, 페이지 전체의 리로드는 발생하지 않습니다.

4. **쿼리 파라미터 추가 및 URL 지정 (`pushQueryParams`)**:
   새로운 쿼리 파라미터를 추가하고, 히스토리에 새 항목을 추가(push)할 수 있습니다. URL 경로를 지정할 수도 있으며, 지정하지 않을 경우 현재 경로에 대해 파라미터가 설정됩니다.

5. **특정 쿼리 파라미터 삭제 (`clearQueryParams` 및 `clearQueryParam`)**:
   `clearQueryParams`는 특정 파라미터를 URL에서 제거하는 함수이며, 여러 파라미터를 한 번에 제거할 수 있습니다. `clearQueryParam`은 단일 파라미터를 제거하는 데 사용됩니다.

6. **모든 쿼리 파라미터 삭제 (`clearAllQueryParams`)**:
   URL의 모든 쿼리 파라미터를 제거하여 쿼리 문자열을 빈 상태로 만듭니다.

---
### props (X)

---
### return

| **Hook/Function**     | **설명**                                                                                                      |
| --------------------- | ----------------------------------------------------------------------------------------------------------- |
| `queryParams`         | 현재 URL의 쿼리 파라미터를 객체 형태로 반환합니다. 쿼리 파라미터가 JSON 형식이라면 자동으로 객체나 배열로 파싱됩니다.                                      |
| `setQueryParams`      | URL의 쿼리 파라미터를 설정합니다. 현재 URL을 대체(replace)하거나, 새 항목을 히스토리에 추가(push)할 수 있습니다. 객체나 배열은 JSON으로 직렬화하여 저장할 수 있습니다. |
| `updateQueryParams`   | 페이지를 새로고침하지 않고 쿼리 파라미터를 업데이트합니다. 이 함수는 shallow 라우팅을 사용하여 URL 쿼리 문자열만 업데이트하고 페이지 이동은 발생하지 않게 합니다.            |
| `pushQueryParams`     | `setQueryParams`와 유사하지만, 이 함수는 히스토리에 새 항목을 추가합니다. 또한, 다른 URL 경로를 지정할 수도 있습니다.                               |
| `clearQueryParams`    | 특정 쿼리 파라미터를 URL에서 제거합니다. 이 함수는 제거할 파라미터 이름들의 배열을 받아들이며, 다른 파라미터는 그대로 유지됩니다.                                 |
| `clearQueryParam`     | 단일 쿼리 파라미터를 URL에서 제거합니다. 특정 하나의 파라미터만 제거할 때 유용합니다.                                                          |
| `clearAllQueryParams` | 모든 쿼리 파라미터를 URL에서 제거하고, 쿼리 문자열을 빈 객체로 초기화합니다.                                                               |

---
### code

```javascript
import { useRouter } from "next/router";
import { useCallback, useMemo } from "react";

export const useQueryParams = () => {
  const router = useRouter();
  const { query } = router;

  // 쿼리 파라미터를 가져오는 부분
  const queryParams = useMemo(() => {
    const params = {};

    Object.keys(query).forEach((key) => {
      try {
        // 쿼리 파라미터가 JSON 형식의 문자열이면 파싱하여 객체나 배열로 변환
        params[key] = JSON.parse(query[key]);
      } catch (e) {
        // JSON 형식이 아니면 그대로 문자열로 저장
        params[key] = query[key];
      }
    });

    return params;
  }, [query]);

  // 쿼리 파라미터를 설정하는 함수 (replace 또는 push 가능)
  const setQueryParams = useCallback(
    (newParams, options = {}) => {
      const {
        method = "replace", // 기본 동작은 'replace', 'push'도 가능
        pathname = router.pathname, // 기본은 현재 주소
        shallow = false,
      } = options;

      const updatedQuery = {
        ...query,
        ...Object.keys(newParams).reduce((acc, key) => {
          acc[key] =
            typeof newParams[key] === "object" ? JSON.stringify(newParams[key]) : newParams[key];
          return acc;
        }, {}),
      };

      Object.keys(updatedQuery).forEach((key) => {
        if (updatedQuery[key] === null || updatedQuery[key] === undefined) {
          delete updatedQuery[key];
        }
      });

      const methodToUse = method === "push" ? "push" : "replace";
      router[methodToUse](
        {
          pathname: pathname,
          query: updatedQuery,
        },
        undefined,
        { shallow },
      );
    },
    [router, query],
  );

  // 페이지 이동 없이 URL만 shallow 업데이트
  const updateQueryParams = useCallback(
    (newParams) => {
      const updatedQuery = {
        ...query,
        ...Object.keys(newParams).reduce((acc, key) => {
          acc[key] =
            typeof newParams[key] === "object" ? JSON.stringify(newParams[key]) : newParams[key];
          return acc;
        }, {}),
      };

      Object.keys(updatedQuery).forEach((key) => {
        if (updatedQuery[key] === null || updatedQuery[key] === undefined) {
          delete updatedQuery[key];
        }
      });

      router.replace(
        {
          pathname: router.pathname,
          query: updatedQuery,
        },
        undefined,
        { shallow: true },
      );
    },
    [router, query],
  );

  // router.push로 쿼리 파라미터를 설정, URL을 지정 가능
  const pushQueryParams = useCallback(
    (newParams, url = router.pathname) => {
      const updatedQuery = {
        ...query,
        ...Object.keys(newParams).reduce((acc, key) => {
          acc[key] =
            typeof newParams[key] === "object" ? JSON.stringify(newParams[key]) : newParams[key];
          return acc;
        }, {}),
      };

      Object.keys(updatedQuery).forEach((key) => {
        if (updatedQuery[key] === null || updatedQuery[key] === undefined) {
          delete updatedQuery[key];
        }
      });

      router.push(
        {
          pathname: url,
          query: updatedQuery,
        },
        undefined,
        { shallow: false },
      );
    },
    [router, query],
  );

  // 단일 쿼리 파라미터를 제거하는 함수
  const clearQueryParam = useCallback(
    (paramToClear) => {
      const updatedQuery = { ...query };
      delete updatedQuery[paramToClear];

      router.replace(
        {
          pathname: router.pathname,
          query: updatedQuery,
        },
        undefined,
        { shallow: false },
      );
    },
    [router, query],
  );

  // 특정 쿼리 파라미터들을 제거하는 함수
  const clearQueryParams = useCallback(
    (paramsToClear) => {
      const updatedQuery = { ...query };

      paramsToClear.forEach((param) => {
        delete updatedQuery[param];
      });

      router.replace(
        {
          pathname: router.pathname,
          query: updatedQuery,
        },
        undefined,
        { shallow: false },
      );
    },
    [router, query],
  );

  // 모든 쿼리 파라미터를 제거하는 함수
  const clearAllQueryParams = useCallback(() => {
    router.replace(
      {
        pathname: router.pathname,
        query: {},
      },
      undefined,
      { shallow: false },
    );
  }, [router]);

  return {
    queryParams,
    setQueryParams,
    updateQueryParams,
    pushQueryParams,
    clearQueryParams,
    clearQueryParam,
    clearAllQueryParams,
  };
};
```

---
### example

```javascript
import { useQueryParams } from 'useQueryParams 경로'
import { useLogger } from "react-use";

const MyComponent = () => {
  const {
    queryParams,
    setQueryParams,
    updateQueryParams,
    pushQueryParams,
    clearQueryParams,
    clearQueryParam,
    clearAllQueryParams,
  } = useQueryParams();

  // 쿼리 파라미터를 replace로 설정
  const handleSetWithReplace = () => {
    setQueryParams(
      { page: 2, sort: "desc", filters: { category: "books", priceRange: [10, 50] } },
      { method: "replace" },
    );
  };

  // 쿼리 파라미터를 push로 설정
  const handleSetWithPush = () => {
    pushQueryParams(
      { user: { id: 123, name: "John" } },
      // "/profile", // URL을 명시적으로 지정
    );
  };

  // 쿼리 파라미터를 shallow 업데이트
  const handleUpdateQueryParams = () => {
    updateQueryParams({ search: "react" });
  };

  // 특정 쿼리 파라미터를 제거
  const handleClearSpecificParams = () => {
    clearQueryParams(["sort", "filters"]);
  };

  // 단일 쿼리 파라미터를 제거
  const handleClearSingleParam = () => {
    clearQueryParam("search");
  };

  // 모든 쿼리 파라미터를 제거
  const handleClearAllParams = () => {
    clearAllQueryParams();
  };

  return (
    <div>
      <h1>Query Params</h1>
      <pre>{JSON.stringify(queryParams, null, 2)}</pre>

      <button onClick={handleSetWithReplace}>Set Query Params with Replace</button>
      <button onClick={handleSetWithPush}>Set Query Params with Push</button>
      <button onClick={handleUpdateQueryParams}>Update Query Params (Shallow)</button>
      <button onClick={handleClearSpecificParams}>Clear Specific Params</button>
      <button onClick={handleClearSingleParam}>Clear Single Param</button>
      <button onClick={handleClearAllParams}>Clear All Params</button>
    </div>
  );
};

export default MyComponent;
```