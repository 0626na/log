> 이 글은 chat gpt로 번역한 Next.js 공식문서 입니다. 오직 개인의 학습을 위함이 목적입니다.
> 원문: [layout.js](https://nextjs.org/docs/app/api-reference/file-conventions/layout)

Next.js 애플리케이션에서 레이아웃을 정의하기 위해 사용하는 파일입니다.

app/dashboard/layout.tsx

```tsx
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return <section>{children}</section>
}
```

**루트 레이아웃**은 루트 `app` 디렉토리에서 최상위에 위치하는 레이아웃입니다. 이는 `<html>` 및 `<body>` 태그와 전역적으로 공유되는 UI를 정의하는 데 사용됩니다.

app/layout.tsx

```tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```

## Reference

### Props

#### `children` (required)

레이아웃 컴포넌트는 `children` prop을 받아 사용해야 합니다. 렌더링 시, `children`에는 해당 레이아웃이 감싸고 있는 경로 세그먼트가 채워집니다. 이는 주로 자식 [[page.js|Layout]] (존재할 경우) 또는 [[page.js|Page]]의 컴포넌트가 되지만, 상황에 따라 [Loading](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming)이나 [Error](https://nextjs.org/docs/app/building-your-application/routing/error-handling)와 같은 다른 특별 파일이 될 수도 있습니다.

#### `params` (optional)

루트 세그먼트부터 해당 레이아웃까지의 [동적 라우트 매개변수](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes) 객체를 포함하는 객체로 해결되는 promise입니다.

app/dashboard/\[team\]/layout.tsx

```tsx
export default async function Layout({
  params,
}: {
  params: Promise<{ team: string }>
}) {
  const team = (await params).team
}
```

|예시 경로|URL|`params`|
|---|---|---|
|`app/dashboard/[team]/layout.js`|`/dashboard/1`|`Promise<{ team: '1' }>`|
|`app/shop/[tag]/[item]/layout.js`|`/shop/1/2`|`Promise<{ tag: '1', item: '2' }>`|
|`app/blog/[...slug]/layout.js`|`/blog/1/2`|`Promise<{ slug: ['1', '2'] }>`|

- `params` prop는 promise이므로, 값을 접근하기 위해 `async/await` 또는 React의 [`use`](https://react.dev/reference/react/use) 함수를 사용해야 합니다.
    - Next.js 14 이하 버전에서는 `params`가 동기 prop이었습니다. 이전 버전과의 호환성을 위해 Next.js 15에서도 여전히 동기적으로 접근할 수 있지만, 이 방식은 향후 deprecated 될 예정입니다.

### Root Layouts

app 디렉토리에는 반드시 루트 `app/layout.js`가 포함되어야 합니다.

app/layout.tsx

```tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html>
      <body>{children}</body>
    </html>
  )
}
```

- 루트 레이아웃은 반드시 `<html>` 및 `<body>` 태그를 정의해야 합니다.
    - 루트 레이아웃에 `<title>`이나 `<meta>`와 같은 `<head>` 태그를 수동으로 추가해서는 안 됩니다. 대신, [Metadata API](https://nextjs.org/docs/app/building-your-application/optimizing/metadata)를 사용하여 스트리밍 및 `<head>` 요소 중복 제거와 같은 고급 요구사항을 자동으로 처리해야 합니다.
- [route groups](https://nextjs.org/docs/app/building-your-application/routing/route-groups)를 사용하여 여러 개의 루트 레이아웃을 생성할 수 있습니다.
    - 여러 루트 레이아웃 간 이동은 클라이언트 사이드 네비게이션이 아닌 **전체 페이지 로드**를 발생시킵니다. 예를 들어, `app/(shop)/layout.js`를 사용하는 `/cart`에서 `app/(marketing)/layout.js`를 사용하는 `/blog`로 이동하면 전체 페이지 로드가 발생합니다. 이는 여러 루트 레이아웃에만 해당됩니다.

## Caveats

### 레이아웃은 `searchParams`를 받지 않습니다

[[page.js|Pages]]와 달리, 레이아웃 컴포넌트는 `searchParams` prop을 받지 않습니다. 이는 공유 레이아웃이 네비게이션 중에 [[1. Linking and Navigating#4. 부분 렌더링 (Partial Rendering)|다시 렌더링 되지 않기]] 때문에, 네비게이션 간에 `searchParams`가 오래된 상태가 될 수 있기 때문입니다.

클라이언트 사이드 네비게이션을 사용할 경우, Next.js는 두 경로 사이의 공통 레이아웃 아래 부분만 자동으로 렌더링합니다.

예를 들어, 아래의 디렉토리 구조에서 `dashboard/layout.tsx`는 `/dashboard/settings`와 `/dashboard/analytics` 모두에 대한 공통 레이아웃입니다:

![File structure showing a dashboard folder nesting a layout.tsx file, and settings and analytics folders with their own pages](https://nextjs.org/_next/image?url=%2Fdocs%2Flight%2Fshared-dashboard-layout.png&w=3840&q=75)

`/dashboard/settings`에서 `/dashboard/analytics`로 이동할 때, `/dashboard/analytics`의 `page.tsx`는 서버에서 다시 렌더링되지만, 두 경로 간에 공유되는 공통 UI인 `dashboard/layout.tsx`는 **다시 렌더링되지 않습니다.**

이러한 성능 최적화 덕분에, 공통 레이아웃을 공유하는 페이지 간의 네비게이션은 페이지의 데이터 페칭 및 렌더링만 수행하면 되므로 전체 경로(공통 레이아웃이 자체 데이터를 페칭할 수 있음)를 다시 렌더링하는 것보다 빠르게 이루어집니다.

`dashboard/layout.tsx`가 다시 렌더링되지 않기 때문에, 네비게이션 후 레이아웃 서버 컴포넌트의 `searchParams` prop은 **오래된 상태**가 될 수 있습니다.

대신, 최신 `searchParams`와 함께 클라이언트에서 다시 렌더링되는 레이아웃 내 클라이언트 컴포넌트에서 Page [[page.js#`searchParams` (선택 사항)|searchParams]] prop 또는 [[useSearchParams]] 훅을 사용하십시오.

### 레이아웃은 `pathname`에 접근할 수 없습니다

레이아웃은 `pathname`에 접근할 수 없습니다. 이는 레이아웃이 기본적으로 서버 컴포넌트이며, 클라이언트 사이드 네비게이션 중에  [[1. Linking and Navigating#4. 부분 렌더링 (Partial Rendering)|다시 렌더링 되지 않기]] 때문에 네비게이션 간에 `pathname`이 오래된 상태가 될 수 있기 때문입니다. 오래된 상태를 방지하려면, Next.js가 경로의 모든 세그먼트를 다시 페치해야 하므로 캐싱의 이점을 잃고 네비게이션 시 [RSC 페이로드](https://nextjs.org/docs/app/building-your-application/rendering/server-components#what-is-the-react-server-component-payload-rsc) 크기가 증가하게 됩니다.

대신, `pathname`에 의존하는 로직을 클라이언트 컴포넌트로 분리하여 레이아웃에 임포트할 수 있습니다. 클라이언트 컴포넌트는 네비게이션 중에 다시 렌더링(하지만 다시 페치되지는 않음)되므로, [[usePathname]]과 같은 Next.js 훅을 사용하여 현재 `pathname`에 접근하고 오래된 상태를 방지할 수 있습니다.

app/dashboard/layout.tsx

```tsx
import { ClientComponent } from '@/app/ui/ClientComponent'
 
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <>
      <ClientComponent />
      {/* Other Layout UI */}
      <main>{children}</main>
    </>
  )
}
```

일반적인 `pathname` 패턴은 또한 [[layout.js#`params` (optional)|params]] [`params`](https://nextjs.org/docs/app/api-reference/file-conventions/layout#params-optional) prop을 사용하여 구현할 수 있습니다.

자세한 내용은 [예제](https://nextjs.org/docs/app/building-your-application/routing/layouts-and-templates#examples) 섹션을 참고하세요.

## Examples

### `params`를 기반으로 콘텐츠 표시하기

[동적 라우트 세그먼트](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes)를 사용하여, `params` prop을 기반으로 특정 콘텐츠를 표시하거나 가져올 수 있습니다.

app/dashboard/layout.tsx

```tsx
export default async function DashboardLayout({
  children,
  params,
}: {
  children: React.ReactNode
  params: Promise<{ team: string }>
}) {
  const { team } = await params
 
  return (
    <section>
      <header>
        <h1>Welcome to {team}'s Dashboard</h1>
      </header>
      <main>{children}</main>
    </section>
  )
}
```

### 클라이언트 컴포넌트에서 `params` 읽기

클라이언트 컴포넌트(비동기 처리를 할 수 없는)에서 `params`를 사용하려면, React의 [`use`](https://react.dev/reference/react/use) 함수를 사용하여 promise를 읽을 수 있습니다.

app/page.tsx

```tsx
'use client'
 
import { use } from 'react'
 
export default function Page({
  params,
}: {
  params: Promise<{ slug: string }>
}) {
  const { slug } = use(params)
}
```

#nextJS #공식문서 