### 사용조건

- `react`
- `next.js page router`

위 두조건을 사용하고 있는 프로젝트에서 다음 `useQueryParams` hook을 활용할 수 있습니다

``` jsx
import { useRouter } from "next/router";
import { useCallback, useMemo } from "react";

const useQueryParams = () => {

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
  

	// 특정 쿼리 파라미터를 제거하는 함수
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

  

useLogger("queryParams", queryParams);

  

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