> 이 글은 chat gpt o3 mini 모델로 번역한 Next.js 공식문서 입니다. 오직 개인의 학습을 위함이 목적입니다.
> 원문: [usePathname](https://nextjs.org/docs/app/api-reference/functions/use-pathname) 

`usePathname`은 현재 URL의 **pathname**을 읽을 수 있게 해주는 **Client Component** 전용 훅입니다.

app/example-client-component.tsx

```tsx
'use client'
 
import { usePathname } from 'next/navigation'
 
export default function ExampleClientComponent() {
  const pathname = usePathname()
  return <p>현재 경로: {pathname}</p>
}
```

`usePathname`은 의도적으로 [Client Component](https://nextjs.org/docs/app/building-your-application/rendering/client-components)를 사용하도록 요구합니다. Client Component가 성능 저하를 일으키는 것이 아니라, 오히려 [Server Components](https://nextjs.org/docs/app/building-your-application/rendering/server-components) 아키텍처의 필수적인 부분임을 주의해야 합니다.

예를 들어, `usePathname`을 사용하는 Client Component는 초기 페이지 로드 시 HTML로 렌더링됩니다. 새로운 경로로 이동할 때 이 컴포넌트를 다시 불러올 필요가 없으며, 해당 컴포넌트는 한 번 다운로드되어(클라이언트 자바스크립트 번들 내에) 현재 상태에 따라 다시 렌더링됩니다.

> **알아두면 좋은 점**:
> 
> - [Server Component](https://nextjs.org/docs/app/building-your-application/rendering/server-components)에서 현재 URL을 읽는 것은 지원되지 않습니다. 이는 페이지 전환 시 레이아웃 상태를 유지하기 위한 의도적인 설계입니다.
> - 호환성 모드:
>     - `usePathname`은 [fallback route](https://nextjs.org/docs/pages/api-reference/functions/get-static-paths#fallback-true)가 렌더링 중이거나 Next.js에 의해 `pages` 디렉토리 페이지가 [자동으로 정적 최적화](https://nextjs.org/docs/pages/building-your-application/rendering/automatic-static-optimization)된 상태에서 라우터가 준비되지 않은 경우 `null`을 반환할 수 있습니다.
>     - `next.config`의 [rewrites](https://nextjs.org/docs/app/api-reference/config/next-config-js/rewrites)나 [`Middleware`](https://nextjs.org/docs/app/building-your-application/routing/middleware)와 함께 `usePathname`을 사용할 때는 hydration mismatch 오류를 방지하기 위해 `useState`와 `useEffect`도 함께 사용되어야 합니다. 자세한 내용은 [rewrites 예제](https://github.com/vercel/next.js/tree/canary/examples/rewrites)를 참조하세요.
>     - 프로젝트 내에 `app` 디렉토리와 `pages` 디렉토리가 모두 존재하면, Next.js가 자동으로 타입을 업데이트합니다.

## Parameters

```tsx
const pathname = usePathname()
```

## Returns

`usePathname`은 현재 URL의 pathname 문자열을 반환합니다. 예를 들어:

|URL|반환값|
|---|---|
|`/`|`'/'`|
|`/dashboard`|`'/dashboard'`|
|`/dashboard?v=2`|`'/dashboard'`|
|`/blog/hello-world`|`'/blog/hello-world'`|

## Examples

### 경로 변경에 반응하여 어떤 동작 수행하기

app/example-client-component.tsx

```tsx
'use client'
 
import { usePathname, useSearchParams } from 'next/navigation'
 
function ExampleClientComponent() {
  const pathname = usePathname()
  const searchParams = useSearchParams()
  useEffect(() => {
    // 여기서 어떤 동작을 수행합니다...
  }, [pathname, searchParams])
}
```

#nextJS #공식문서 