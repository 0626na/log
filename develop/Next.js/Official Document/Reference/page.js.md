> 이 글은 chat gpt o3 mini 모델로 번역한 Next.js 공식문서 입니다. 오직 개인의 학습을 위함이 목적입니다.
> 원문: [page.js](https://nextjs.org/docs/app/api-reference/file-conventions/page)

`page` 파일은 특정 라우트에 **고유한** UI를 정의할 수 있게 해줍니다. 파일에서 컴포넌트를 기본으로 내보내서 페이지를 생성할 수 있습니다:

app/blog/\[slug\]/page.tsx
```tsx
export default function Page({
  params,
  searchParams,
}: {
  params: Promise<{ slug: string }>
  searchParams: Promise<{ [key: string]: string | string[] | undefined }>
}) {
  return <h1>My Page</h1>
}
```

## 알아두면 좋은 점

- `page`에는 `.js`, `.jsx`, 또는 `.tsx` 파일 확장자를 사용할 수 있습니다.
- `page`는 항상 라우트 하위 트리의 **말단(leaf)** 입니다.
- 라우트 세그먼트를 **공개적으로 접근 가능**하게 만들기 위해 `page` 파일이 필요합니다.
- 페이지는 기본적으로 [서버 컴포넌트](https://react.dev/reference/rsc/server-components)이지만, [클라이언트 컴포넌트](https://react.dev/reference/rsc/use-client)로 설정할 수 있습니다.

## 참고

### 속성

#### `params` (선택 사항)

루트 세그먼트부터 해당 페이지까지의 [동적 라우트 매개변수](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes)를 포함하는 객체로 해결되는 promise입니다.

app/shop/\[slug\]/page.tsx
```tsx
export default async function Page({
  params,
}: {
  params: Promise<{ slug: string }>
}) {
  const slug = (await params).slug
}
```

|Example Route|URL|`params`|
|---|---|---|
|`app/shop/[slug]/page.js`|`/shop/1`|`Promise<{ slug: '1' }>`|
|`app/shop/[category]/[item]/page.js`|`/shop/1/2`|`Promise<{ category: '1', item: '2' }>`|
|`app/shop/[...slug]/page.js`|`/shop/1/2`|`Promise<{ slug: ['1', '2'] }>`|

- `params` 속성은 promise이므로, 값을 접근하기 위해 반드시 `async/await` 또는 React의 [`use`](https://react.dev/reference/react/use) 함수를 사용해야 합니다.
    - 버전 14 및 이전에서는 `params`가 동기 속성이었습니다. 이전 버전과의 호환성을 위해 Next.js 15에서도 동기적으로 접근할 수 있지만, 이 동작은 향후 더 이상 지원되지 않을 예정입니다.

#### `searchParams` (선택 사항)

현재 URL의 [검색 매개변수](https://developer.mozilla.org/docs/Learn/Common_questions/What_is_a_URL#parameters)를 포함하는 객체로 해결되는 promise입니다. 예를 들어:

app/shop/page.tsx
```tsx
export default async function Page({
  searchParams,
}: {
  searchParams: Promise<{ [key: string]: string | string[] | undefined }>
}) {
  const filters = (await searchParams).filters
}
```

|Example URL|`searchParams`|
|---|---|
|`/shop?a=1`|`Promise<{ a: '1' }>`|
|`/shop?a=1&b=2`|`Promise<{ a: '1', b: '2' }>`|
|`/shop?a=1&a=2`|`Promise<{ a: ['1', '2'] }>`|

- `searchParams` 속성은 promise이므로, 값을 접근하기 위해 반드시 `async/await` 또는 React의 [`use`](https://react.dev/reference/react/use) 함수를 사용해야 합니다.
    - 버전 14 및 이전에서는 `searchParams`가 동기 속성이었습니다. 이전 버전과의 호환성을 위해 Next.js 15에서도 동기적으로 접근할 수 있지만, 이 동작은 향후 더 이상 지원되지 않을 예정입니다.
- `searchParams`는 미리 알 수 없는 값을 가진 **[동적 API](https://nextjs.org/docs/app/building-your-application/rendering/server-components#dynamic-apis)** 입니다. 이를 사용하면 요청 시 페이지가 **[동적 렌더링](https://nextjs.org/docs/app/building-your-application/rendering/server-components#dynamic-rendering)** 으로 전환됩니다.
- `searchParams`는 `URLSearchParams` 인스턴스가 아닌 일반 JavaScript 객체입니다.

## 예제

### `params`를 기반으로 콘텐츠 표시하기

[동적 라우트 세그먼트](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes)를 사용하여, `params` 속성을 기반으로 페이지의 특정 콘텐츠를 표시하거나 가져올 수 있습니다.

app/blog/\[slug\]/page.tsx
```tsx
export default async function Page({
  params,
}: {
  params: Promise<{ slug: string }>
}) {
  const { slug } = await params
  return <h1>Blog Post: {slug}</h1>
}
```

### `searchParams`를 사용한 필터링 처리

URL의 쿼리 문자열을 기반으로 필터링, 페이지네이션 또는 정렬을 처리하기 위해 `searchParams` 속성을 사용할 수 있습니다.

app/shop/page.tsx
```tsx
export default async function Page({
  searchParams,
}: {
  searchParams: Promise<{ [key: string]: string | string[] | undefined }>
}) {
  const { page = '1', sort = 'asc', query = '' } = await searchParams
 
  return (
    <div>
      <h1>Product Listing</h1>
      <p>Search query: {query}</p>
      <p>Current page: {page}</p>
      <p>Sort order: {sort}</p>
    </div>
  )
}
```

### 클라이언트 컴포넌트에서 `searchParams`와 `params` 읽기

비동기 처리가 불가능한 클라이언트 컴포넌트에서 `searchParams`와 `params`를 사용하기 위해, React의 [`use`](https://react.dev/reference/react/use) 함수를 사용하여 promise를 읽을 수 있습니다:

app/page.tsx
```tsx
'use client'
 
import { use } from 'react'
 
export default function Page({
  params,
  searchParams,
}: {
  params: Promise<{ slug: string }>
  searchParams: Promise<{ [key: string]: string | string[] | undefined }>
}) {
  const { slug } = use(params)
  const { query } = use(searchParams)
}
```


#공식문서 #nextJS 