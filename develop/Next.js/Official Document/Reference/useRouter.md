> 이 글은 chat gpt o3 mini 모델로 번역한 Next.js 공식문서 입니다. 오직 개인의 학습을 위함이 목적입니다.
> 원문: [userRouter](https://nextjs.org/docs/app/api-reference/functions/use-router) 

`useRouter` 훅은 [클라이언트 컴포넌트](https://nextjs.org/docs/app/building-your-application/rendering/client-components) 내부에서 프로그램적으로 라우트를 변경할 수 있게 해줍니다.

> **추천:** 특별한 이유가 없다면 내비게이션에는 [`<Link>` 컴포넌트](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#link-component)를 사용하세요.

app/example-client-component.tsx

```tsx
'use client'
 
import { useRouter } from 'next/navigation'
 
export default function Page() {
  const router = useRouter()
 
  return (
    <button type="button" onClick={() => router.push('/dashboard')}>
      Dashboard
    </button>
  )
}
```

## [`useRouter()`](https://nextjs.org/docs/app/api-reference/functions/use-router#userouter)

- `router.push(href: string, { scroll: boolean })`: 제공된 경로로 클라이언트 측 내비게이션을 수행합니다. 브라우저의 [히스토리](https://developer.mozilla.org/docs/Web/API/History_API) 스택에 새로운 항목을 추가합니다.
- `router.replace(href: string, { scroll: boolean })`: 브라우저의 [히스토리 스택](https://developer.mozilla.org/docs/Web/API/History_API)에 새로운 항목을 추가하지 않고 제공된 경로로 클라이언트 측 내비게이션을 수행합니다.
- `router.refresh()`: 현재 경로를 새로 고칩니다. 서버에 새로운 요청을 보내고, 데이터 요청을 다시 가져오며, 서버 컴포넌트를 재렌더링합니다. 클라이언트는 영향을 받지 않는 클라이언트 측 리액트 상태(예: `useState`)나 브라우저 상태(예: 스크롤 위치)를 잃지 않고 업데이트된 리액트 서버 컴포넌트 페이로드를 병합합니다.
- `router.prefetch(href: string)`: 제공된 경로를 미리 가져와 클라이언트 측 전환을 빠르게 합니다.
- `router.back()`: 브라우저의 히스토리 스택에서 이전 경로로 이동합니다.
- `router.forward()`: 브라우저의 히스토리 스택에서 다음 페이지로 이동합니다.

> **알아두면 좋은 사항**:
> 
> - 신뢰할 수 없거나 검증되지 않은 URL을 `router.push` 또는 `router.replace`에 전달해서는 안 됩니다. 이는 사이트를 교차 사이트 스크립팅(XSS) 취약점에 노출시킬 수 있습니다. 예를 들어, `javascript:` URL을 `router.push` 또는 `router.replace`에 전달하면 해당 URL이 페이지의 컨텍스트 내에서 실행됩니다.
> - `<Link>` 컴포넌트는 뷰포트에 보일 때 자동으로 경로를 미리 가져옵니다.
> - fetch 요청이 캐시된 경우, `refresh()`는 동일한 결과를 재생산할 수 있습니다. `cookies`나 `headers`와 같은 다른 동적 API 역시 응답을 변경할 수 있습니다.

### [Migrating from `next/router`](https://nextjs.org/docs/app/api-reference/functions/use-router#migrating-from-nextrouter)

- App Router 사용 시, `useRouter` 훅은 `next/router`가 아닌 `next/navigation`에서 임포트해야 합니다.
- `pathname` 문자열은 제거되었으며, 대신 [`usePathname()`](https://nextjs.org/docs/app/api-reference/functions/use-pathname)으로 대체되었습니다.
- `query` 객체는 제거되었으며, 대신 [`useSearchParams()`](https://nextjs.org/docs/app/api-reference/functions/use-search-params)로 대체되었습니다.
- `router.events`는 대체되었습니다. [아래 참조.](https://nextjs.org/docs/app/api-reference/functions/use-router#router-events)

[전체 마이그레이션 가이드 보기](https://nextjs.org/docs/app/building-your-application/upgrading/app-router-migration).

## [Examples](https://nextjs.org/docs/app/api-reference/functions/use-router#examples)

### [라우터 이벤트](https://nextjs.org/docs/app/api-reference/functions/use-router#router-events)

다른 클라이언트 컴포넌트 훅들인 `usePathname`와 `useSearchParams`를 조합하여 페이지 변경 이벤트를 감지할 수 있습니다.

app/components/navigation-events.js

```tsx
'use client'
 
import { useEffect } from 'react'
import { usePathname, useSearchParams } from 'next/navigation'
 
export function NavigationEvents() {
  const pathname = usePathname()
  const searchParams = useSearchParams()
 
  useEffect(() => {
    const url = `${pathname}?${searchParams}`
    console.log(url)
    // 현재 URL을 사용할 수 있습니다.
    // ...
  }, [pathname, searchParams])
 
  return '...'
}
```

레이아웃에 임포트할 수 있습니다.

app/layout.js

```tsx
import { Suspense } from 'react'
import { NavigationEvents } from './components/navigation-events'
 
export default function Layout({ children }) {
  return (
    <html lang="en">
      <body>
        {children}
 
        <Suspense fallback={null}>
          <NavigationEvents />
        </Suspense>
      </body>
    </html>
  )
}
```

> **알아두면 좋은 사항**: `<NavigationEvents>`는 [`Suspense` 경계](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming#example)로 감싸져 있습니다. 이는 [`useSearchParams()`](https://nextjs.org/docs/app/api-reference/functions/use-search-params)가 [정적 렌더링](https://nextjs.org/docs/app/building-your-application/rendering/server-components#static-rendering-default) 동안 가장 가까운 `Suspense` 경계까지 클라이언트 측 렌더링을 유발하기 때문입니다. [자세히 알아보기](https://nextjs.org/docs/app/api-reference/functions/use-search-params#behavior).
- 문서내 링크: [[3. Loading UI and Streaming#[예시](https //nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming example)| Suspense 경계 예시]]

### [스크롤 최상단 이동 비활성화](https://nextjs.org/docs/app/api-reference/functions/use-router#disabling-scroll-to-top)

기본적으로 Next.js는 새 경로로 이동할 때 페이지 상단으로 스크롤합니다. 이 동작은 `router.push()` 또는 `router.replace()`에 `scroll: false` 옵션을 전달하여 비활성화할 수 있습니다.

app/example-client-component.tsx

```tsx
'use client'
 
import { useRouter } from 'next/navigation'
 
export default function Page() {
  const router = useRouter()
 
  return (
    <button
      type="button"
      onClick={() => router.push('/dashboard', { scroll: false })}
    >
      Dashboard
    </button>
  )
}
```

#nextJS #공식문서 