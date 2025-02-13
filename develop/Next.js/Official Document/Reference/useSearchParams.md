> 이 글은 chat gpt로 번역한 Next.js 공식문서 입니다. 오직 개인의 학습을 위함이 목적입니다.
> 원문: [useSearchParams](https://nextjs.org/docs/app/api-reference/functions/use-search-params)
 
`useSearchParams`는 현재 URL의 **쿼리 스트링**을 읽을 수 있게 해주는 **클라이언트 컴포넌트** 훅입니다.

`useSearchParams`는 [`URLSearchParams`](https://developer.mozilla.org/docs/Web/API/URLSearchParams) 인터페이스의 **읽기 전용** 버전을 반환합니다.

app/dashboard/search-bar.tsx

```tsx
'use client'
 
import { useSearchParams } from 'next/navigation'
 
export default function SearchBar() {
  const searchParams = useSearchParams()
 
  const search = searchParams.get('search')
 
  // URL 예시 -> `/dashboard?search=my-project`
  // `search`의 값 -> 'my-project'
  return <>Search: {search}</>
}
```

## [Parameters](https://nextjs.org/docs/app/api-reference/functions/use-search-params#parameters)

```tsx
const searchParams = useSearchParams()
```

## [Returns](https://nextjs.org/docs/app/api-reference/functions/use-search-params#returns)

`useSearchParams`는 URL의 쿼리 스트링을 읽기 위한 유틸리티 메서드를 포함한 [`URLSearchParams`](https://developer.mozilla.org/docs/Web/API/URLSearchParams) 인터페이스의 **읽기 전용** 버전을 반환합니다:

- [`URLSearchParams.get()`](https://developer.mozilla.org/docs/Web/API/URLSearchParams/get): 검색 파라미터와 연결된 첫 번째 값을 반환합니다. 예를 들어:

|URL|`searchParams.get("a")`|
|---|---|
|`/dashboard?a=1`|`'1'`|
|`/dashboard?a=`|`''`|
|`/dashboard?b=3`|`null`|
|`/dashboard?a=1&a=2`|`'1'` _- 모든 값을 얻으려면 [`getAll()`](https://developer.mozilla.org/docs/Web/API/URLSearchParams/getAll)를 사용하세요_|

- [`URLSearchParams.has()`](https://developer.mozilla.org/docs/Web/API/URLSearchParams/has): 주어진 파라미터가 존재하는지 여부를 나타내는 불리언 값을 반환합니다. 예를 들어:

|URL|`searchParams.has("a")`|
|---|---|
|`/dashboard?a=1`|`true`|
|`/dashboard?b=3`|`false`|

- [`URLSearchParams`](https://developer.mozilla.org/docs/Web/API/URLSearchParams)의 다른 **읽기 전용** 메서드들에 대해 더 알아보세요. 여기에는 [`getAll()`](https://developer.mozilla.org/docs/Web/API/URLSearchParams/getAll), [`keys()`](https://developer.mozilla.org/docs/Web/API/URLSearchParams/keys), [`values()`](https://developer.mozilla.org/docs/Web/API/URLSearchParams/values), [`entries()`](https://developer.mozilla.org/docs/Web/API/URLSearchParams/entries), [`forEach()`](https://developer.mozilla.org/docs/Web/API/URLSearchParams/forEach), 그리고 [`toString()`](https://developer.mozilla.org/docs/Web/API/URLSearchParams/toString)가 포함됩니다.

> **참고 사항**:
> 
> - `useSearchParams`는 [클라이언트 컴포넌트](https://nextjs.org/docs/app/building-your-application/rendering/client-components) 훅이며, [서버 컴포넌트](https://nextjs.org/docs/app/building-your-application/rendering/server-components)에서는 지원되지 않습니다. 이는 [부분 렌더링](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#4-partial-rendering) 중에 오래된 값이 발생하는 것을 방지하기 위함입니다.
> - 만약 애플리케이션에 `/pages` 디렉토리가 포함되어 있다면, `useSearchParams`는 `ReadonlyURLSearchParams | null`을 반환합니다. `null` 값은 `getServerSideProps`를 사용하지 않는 페이지의 프리렌더링 시 검색 파라미터를 알 수 없기 때문에 마이그레이션 호환성을 위해 제공됩니다.

## [Behavior](https://nextjs.org/docs/app/api-reference/functions/use-search-params#behavior)

### [Static Rendering](https://nextjs.org/docs/app/api-reference/functions/use-search-params#static-rendering)

만약 라우트가 [정적으로 렌더링](https://nextjs.org/docs/app/building-your-application/rendering/server-components#static-rendering-default)된다면, `useSearchParams`를 호출할 경우 가장 가까운 [`Suspense` 경계](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming#example)까지의 클라이언트 컴포넌트 트리가 클라이언트 측에서 렌더링됩니다.

이렇게 하면 `useSearchParams`를 사용하는 동적 부분은 클라이언트 측에서 렌더링되는 반면, 라우트의 일부는 정적으로 렌더링될 수 있습니다.

`useSearchParams`를 사용하는 클라이언트 컴포넌트를 `<Suspense/>` 경계로 감싸는 것을 권장합니다. 이렇게 하면 해당 컴포넌트 위의 모든 클라이언트 컴포넌트가 정적으로 렌더링되어 초기 HTML의 일부로 전송될 수 있습니다. [예시](https://nextjs.org/docs/app/api-reference/functions/use-search-params#static-rendering).

예를 들어:

app/dashboard/search-bar.tsx

```tsx
'use client'
 
import { useSearchParams } from 'next/navigation'
 
export default function SearchBar() {
  const searchParams = useSearchParams()
 
  const search = searchParams.get('search')
 
  // 정적 렌더링을 사용할 때는 서버에서 로그에 출력되지 않습니다.
  console.log(search)
 
  return <>Search: {search}</>
}
```

app/dashboard/page.tsx

```tsx
import { Suspense } from 'react'
import SearchBar from './search-bar'
 
// 이 컴포넌트는 Suspense 경계의 fallback으로 전달되며,
// 초기 HTML에서 검색 바 대신 렌더링됩니다.
// React 하이드레이션 동안 값이 사용 가능해지면 fallback이 `<SearchBar>` 컴포넌트로 대체됩니다.
function SearchBarFallback() {
  return <>placeholder</>
}
 
export default function Page() {
  return (
    <>
      <nav>
        <Suspense fallback={<SearchBarFallback />}>
          <SearchBar />
        </Suspense>
      </nav>
      <h1>Dashboard</h1>
    </>
  )
}
```

### [Dynamic Rendering](https://nextjs.org/docs/app/api-reference/functions/use-search-params#dynamic-rendering)

만약 라우트가 [동적으로 렌더링](https://nextjs.org/docs/app/building-your-application/rendering/server-components#dynamic-rendering)된다면, 클라이언트 컴포넌트의 초기 서버 렌더링 시에 `useSearchParams`를 사용할 수 있습니다.

예를 들어:

app/dashboard/search-bar.tsx

```tsx
'use client'
 
import { useSearchParams } from 'next/navigation'
 
export default function SearchBar() {
  const searchParams = useSearchParams()
 
  const search = searchParams.get('search')
 
  // 초기 렌더링 시 서버에서 로그에 출력되고,
  // 이후 네비게이션에서는 클라이언트에서 로그에 출력됩니다.
  console.log(search)
 
  return <>Search: {search}</>
}
```

app/dashboard/page.tsx

```tsx
import SearchBar from './search-bar'
 
export const dynamic = 'force-dynamic'
 
export default function Page() {
  return (
    <>
      <nav>
        <SearchBar />
      </nav>
      <h1>Dashboard</h1>
    </>
  )
}
```

> **참고 사항**: [`dynamic` 라우트 세그먼트 구성 옵션](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config#dynamic)을 `force-dynamic`으로 설정하면 동적 렌더링을 강제할 수 있습니다.

### [Server Components](https://nextjs.org/docs/app/api-reference/functions/use-search-params#server-components)

#### [Pages](https://nextjs.org/docs/app/api-reference/functions/use-search-params#pages)

[Pages](https://nextjs.org/docs/app/api-reference/file-conventions/page) (서버 컴포넌트)에서 검색 파라미터에 접근하려면, [`searchParams`](https://nextjs.org/docs/app/api-reference/file-conventions/page#searchparams-optional) prop을 사용하세요.

#### [Layouts](https://nextjs.org/docs/app/api-reference/functions/use-search-params#layouts)

Pages와 달리, [Layouts](https://nextjs.org/docs/app/api-reference/file-conventions/layout) (서버 컴포넌트)는 `searchParams` prop을 받지 않습니다. 이는 공유 레이아웃이 [네비게이션 중에 다시 렌더링되지 않기](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#4-partial-rendering) 때문에, 네비게이션 간에 `searchParams`가 오래된 상태로 남을 수 있기 때문입니다. [자세한 설명](https://nextjs.org/docs/app/api-reference/file-conventions/layout#layouts-do-not-receive-searchparams)을 참고하세요.

대신, 최신 `searchParams`로 클라이언트에서 다시 렌더링되는 클라이언트 컴포넌트 내에서 Page의 [`searchParams`](https://nextjs.org/docs/app/api-reference/file-conventions/page) prop 또는 [`useSearchParams`](https://nextjs.org/docs/app/api-reference/functions/use-search-params) 훅을 사용하세요.

## [Examples](https://nextjs.org/docs/app/api-reference/functions/use-search-params#examples)

### [Updating `searchParams`](https://nextjs.org/docs/app/api-reference/functions/use-search-params#updating-searchparams)

새로운 `searchParams`를 설정하기 위해 [`useRouter`](https://nextjs.org/docs/app/api-reference/functions/use-router) 또는 [`Link`](https://nextjs.org/docs/app/api-reference/components/link)을 사용할 수 있습니다. 네비게이션이 수행된 후, 현재의 [`page.js`](https://nextjs.org/docs/app/api-reference/file-conventions/page)는 업데이트된 [`searchParams` prop](https://nextjs.org/docs/app/api-reference/file-conventions/page#searchparams-optional)을 받게 됩니다.

app/example-client-component.tsx

```tsx
'use client'
 
export default function ExampleClientComponent() {
  const router = useRouter()
  const pathname = usePathname()
  const searchParams = useSearchParams()
 
  // 현재의 searchParams와 제공된 키/값 쌍을 병합하여 새로운 searchParams 문자열을 생성합니다.
  const createQueryString = useCallback(
    (name: string, value: string) => {
      const params = new URLSearchParams(searchParams.toString())
      params.set(name, value)
 
      return params.toString()
    },
    [searchParams]
  )
 
  return (
    <>
      <p>Sort By</p>
 
      {/* useRouter 사용 */}
      <button
        onClick={() => {
          // `<pathname>?sort=asc`
          router.push(pathname + '?' + createQueryString('sort', 'asc'))
        }}
      >
        ASC
      </button>
 
      {/* <Link> 사용 */}
      <Link
        href={
          // `<pathname>?sort=desc`
          pathname + '?' + createQueryString('sort', 'desc')
        }
      >
        DESC
      </Link>
    </>
  )
}
```

#nextJS #공식문서 