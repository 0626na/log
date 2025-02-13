> 이 글은 chat gpt로 번역한 Next.js 공식문서 입니다. 오직 개인의 학습을 위함이 목적입니다.
> 원문: [Link](https://nextjs.org/docs/app/api-reference/components/link)

아래는 Next.js 공식문서의 `<Link>` 컴포넌트에 대한 내용을 한국어로 번역한 것입니다. 원문 형식을 그대로 유지하여 번역하였으니, Next.js를 처음 배우는 개발자분들도 쉽게 따라올 수 있을 것입니다.

---


`<Link>`는 HTML `<a>` 엘리먼트를 확장하여 [[1. Linking and Navigating#2. Prefetching|prefetching]]과 라우트 간 클라이언트 사이드 네비게이션을 제공하는 React 컴포넌트입니다. 이것은 Next.js에서 라우트 간 이동하는 기본적인 방법입니다.

Basic usage:

app/page.tsx

```tsx
import Link from 'next/link'
 
export default function Page() {
  return <Link href="/dashboard">Dashboard</Link>
}
```

## Reference

다음의 props를 `<Link>` 컴포넌트에 전달할 수 있습니다:

|Prop|Example|Type|Required|
|---|---|---|---|
|[`href`](https://nextjs.org/docs/app/api-reference/components/link#href-required)|`href="/dashboard"`|String or Object|Yes|
|[`replace`](https://nextjs.org/docs/app/api-reference/components/link#replace)|`replace={false}`|Boolean|-|
|[`scroll`](https://nextjs.org/docs/app/api-reference/components/link#scroll)|`scroll={false}`|Boolean|-|
|[`prefetch`](https://nextjs.org/docs/app/api-reference/components/link#prefetch)|`prefetch={false}`|Boolean or null|-|

> **Good to know**: `<a>` 태그의 `className`이나 `target="_blank"`와 같은 속성들은 `<Link>`의 props로 추가할 수 있으며, 내부적으로 사용되는 `<a>` 엘리먼트에 전달됩니다.

### `href` (required)

이동할 경로나 URL입니다.

app/page.tsx

```tsx
import Link from 'next/link'
 
// /about?name=test 로 이동
export default function Page() {
  return (
    <Link
      href={{
        pathname: '/about',
        query: { name: 'test' },
      }}
    >
      About
    </Link>
  )
}
```

### `replace`

**기본값은 `false`입니다.** `true`로 설정하면, `next/link`는 새로운 URL을 브라우저의 [히스토리](https://developer.mozilla.org/docs/Web/API/History_API) 스택에 추가하는 대신 현재의 히스토리 상태를 대체합니다.

app/page.tsx

```tsx
import Link from 'next/link'
 
export default function Page() {
  return (
    <Link href="/dashboard" replace>
      Dashboard
    </Link>
  )
}
```

### `scroll`

**기본값은 `true`입니다.** Next.js에서 `<Link>`의 기본 스크롤 동작은 브라우저의 뒤로 가기 및 앞으로 가기 네비게이션처럼 **스크롤 위치를 유지**하는 것입니다. 새로운 [[page.js|Page]]로 이동할 때, 해당 Page가 뷰포트에 보이는 한 스크롤 위치는 그대로 유지됩니다. 그러나 만약 Page가 뷰포트에 보이지 않는다면, Next.js는 첫 번째 Page 엘리먼트의 상단으로 스크롤합니다.

`scroll = {false}`인 경우, Next.js는 첫 번째 Page 엘리먼트로 스크롤하려고 시도하지 않습니다.

> **Good to know**: Next.js는 스크롤 동작을 관리하기 전에 `scroll: false` 인지 확인합니다. 만약 스크롤이 활성화되어 있다면, 네비게이션에 필요한 관련 DOM 노드를 식별하고 각 최상위 엘리먼트를 검사합니다. 스크롤이 불가능한 엘리먼트와 렌더링되지 않은 HTML 엘리먼트는 건너뛰며, 여기에는 sticky나 고정(fixed) 위치 요소, 그리고 `getBoundingClientRect`로 계산되어 비가시적인 요소들이 포함됩니다. 이후 Next.js는 뷰포트에 보이는 스크롤 가능한 엘리먼트를 식별할 때까지 형제 엘리먼트를 순차적으로 확인합니다.

app/page.tsx

```tsx
import Link from 'next/link'
 
export default function Page() {
  return (
    <Link href="/dashboard" scroll={false}>
      Dashboard
    </Link>
  )
}
```

### `prefetch

Prefetching은 `<Link />` 컴포넌트가 사용자의 뷰포트에 들어올 때(초기 로드 혹은 스크롤을 통해) 발생합니다. Next.js는 연결된 라우트(지정된 `href`)와 그 데이터를 백그라운드에서 미리 로드하여 클라이언트 사이드 네비게이션 성능을 향상시킵니다. 만약 사용자가 `<Link />` 위에 마우스를 올렸을 때, 미리 로드한 데이터가 만료되었다면 Next.js는 이를 다시 prefetching하려고 시도합니다. **Prefetching은 프로덕션 환경에서만 활성화됩니다.**

다음의 값들을 `prefetch` prop에 전달할 수 있습니다:

- **`null` (기본값)**: Prefetching 동작은 라우트가 정적(static)인지 동적(dynamic)인지에 따라 달라집니다. 정적 라우트의 경우, 전체 라우트(모든 데이터 포함)가 미리 로드됩니다. 동적 라우트의 경우, [`loading.js`](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming#instant-loading-states) 경계가 있는 가장 가까운 세그먼트까지의 부분 라우트가 미리 로드됩니다.
- `true`: 정적 및 동적 라우트 모두에 대해 전체 라우트가 미리 로드됩니다.
- `false`: 뷰포트에 진입하거나 hover할 때 prefetching이 전혀 발생하지 않습니다.

app/page.tsx

```tsx
import Link from 'next/link'
 
export default function Page() {
  return (
    <Link href="/dashboard" prefetch={false}>
      Dashboard
    </Link>
  )
}
```

## Examples

다음 예제들은 다양한 상황에서 `<Link>` 컴포넌트를 어떻게 사용하는지 보여줍니다.

### Linking to dynamic segments)

동적 세그먼트로 링크할 때, 템플릿 리터럴과 인터폴레이션을 사용하여 링크 목록을 생성할 수 있습니다. 예를 들어, 블로그 게시글 목록을 생성하려면:

app/blog/post-list.tsx

```tsx
import Link from 'next/link'
 
interface Post {
  id: number
  title: string
  slug: string
}
 
export default function PostList({ posts }: { posts: Post[] }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>
          <Link href={`/blog/${post.slug}`}>{post.title}</Link>
        </li>
      ))}
    </ul>
  )
}
```

### Checking active links

[[usePathname|userPathname()]]을 사용하여 링크가 활성화되어 있는지 확인할 수 있습니다. 예를 들어, 현재의 `pathname`이 링크의 `href`와 일치하는지 확인하여 활성 링크에 클래스를 추가할 수 있습니다:

app/ui/nav-links.tsx

```tsx
'use client'
 
import { usePathname } from 'next/navigation'
import Link from 'next/link'
 
export function Links() {
  const pathname = usePathname()
 
  return (
    <nav>
      <Link className={`link ${pathname === '/' ? 'active' : ''}`} href="/">
        Home
      </Link>
 
      <Link
        className={`link ${pathname === '/about' ? 'active' : ''}`}
        href="/about"
      >
        About
      </Link>
    </nav>
  )
}
```

### Scrolling to an `id`

네비게이션 시 특정 `id`로 스크롤하고 싶다면, URL에 `#` 해시 링크를 추가하거나 `href` prop에 해시 링크를 전달할 수 있습니다. 이는 `<Link>`가 `<a>` 엘리먼트로 렌더링되기 때문에 가능합니다.

```tsx
<Link href="/dashboard#settings">Settings</Link>
 
// Output
<a href="/dashboard#settings">Settings</a>
```

> **Good to know**:
> 
> - 네비게이션 시, 해당 Page가 뷰포트에 보이지 않을 경우 Next.js는 해당 [[page.js|Page]]로 스크롤합니다.

### Linking to dynamic route segments

동적 라우트 세그먼트의 경우, 템플릿 리터럴을 사용하여 링크의 경로를 생성하는 것이 유용할 수 있습니다.

예를 들어, 동적 라우트인 `app/blog/[slug]/page.js`로의 링크 목록을 생성할 수 있습니다:

app/blog/page.tsx

```tsx
import Link from 'next/link'
 
export default function Page({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>
          <Link href={`/blog/${post.slug}`}>{post.title}</Link>
        </li>
      ))}
    </ul>
  )
}
```

### If the child is a custom component that wraps an `<a>` tag

만약 Link의 자식이 `<a>` 태그를 감싸는 커스텀 컴포넌트라면, 반드시 Link에 `passHref`를 추가해야 합니다. 이는 styled-components와 같은 라이브러리를 사용할 때 필요합니다. 이를 추가하지 않으면 `<a>` 태그에 `href` 속성이 전달되지 않아 사이트의 접근성이 떨어지고 SEO에 영향을 줄 수 있습니다. ESLint를 사용한다면, 올바른 `passHref` 사용을 보장하기 위한 내장 규칙인 `next/link-passhref`가 있습니다.

components/nav-link.tsx

```tsx
import Link from 'next/link'
import styled from 'styled-components'
 
// <a> 태그를 감싸는 커스텀 컴포넌트를 생성합니다.
const RedLink = styled.a`
  color: red;
`
 
function NavLink({ href, name }) {
  return (
    <Link href={href} passHref legacyBehavior>
      <RedLink>{name}</RedLink>
    </Link>
  )
}
 
export default NavLink
```

- 만약 emotion의 JSX pragma 기능(`@jsx jsx`)을 사용한다면, `<a>` 태그를 직접 사용하더라도 반드시 `passHref`를 사용해야 합니다.
- 해당 컴포넌트는 올바른 네비게이션을 위해 `onClick` 속성을 지원해야 합니다.

### Nesting a functional component

만약 Link의 자식이 함수형 컴포넌트라면, `passHref`와 `legacyBehavior`를 사용하는 것 외에도 해당 컴포넌트를 `React.forwardRef`로 감싸야 합니다:

app/page.tsx

```tsx
import Link from 'next/link'
import React from 'react'
 
// MyButton의 props 타입을 정의합니다.
interface MyButtonProps {
  onClick?: React.MouseEventHandler<HTMLAnchorElement>
  href?: string
}
 
// 전달된 ref의 타입을 올바르게 지정하기 위해 React.ForwardRefRenderFunction을 사용합니다.
const MyButton: React.ForwardRefRenderFunction<
  HTMLAnchorElement,
  MyButtonProps
> = ({ onClick, href }, ref) => {
  return (
    <a href={href} onClick={onClick} ref={ref}>
      Click Me
    </a>
  )
}
 
// 컴포넌트를 감싸기 위해 React.forwardRef를 사용합니다.
const ForwardedMyButton = React.forwardRef(MyButton)
 
export default function Page() {
  return (
    <Link href="/about" passHref legacyBehavior>
      <ForwardedMyButton />
    </Link>
  )
}
```

### Replace the URL instead of push

Link 컴포넌트의 기본 동작은 새로운 URL을 히스토리 스택에 추가하는(push) 것입니다. 아래 예제와 같이 replace prop을 사용하면 새로운 항목 추가를 방지할 수 있습니다:

app/page.js

```tsx
import Link from 'next/link'
 
export default function Page() {
  return (
    <Link href="/about" replace>
      About us
    </Link>
  )
}
```

### Disable scrolling to the top of the page

Next.js에서 `<Link>`의 기본 스크롤 동작은 브라우저가 뒤로 가기 및 앞으로 가기 시 처리하는 방식과 같이 **스크롤 위치를 유지하는 것**입니다. 새로운 [[page.js|Page]]로 이동할 때, 해당 Page가 뷰포트에 보이는 한 스크롤 위치는 그대로 유지됩니다.

그러나 Page가 뷰포트에 보이지 않으면, Next.js는 첫 번째 Page 엘리먼트의 상단으로 스크롤합니다. 이 동작을 비활성화하고 싶다면, `<Link>` 컴포넌트에 `scroll={false}`를 전달하거나 `router.push()` 또는 `router.replace()`에 `scroll: false`를 전달할 수 있습니다.

app/page.tsx

```tsx
import Link from 'next/link'
 
export default function Page() {
  return (
    <Link href="/#hashid" scroll={false}>
      Disables scrolling to the top
    </Link>
  )
}
```

`router.push()` 또는 `router.replace()` 사용 예:

```tsx
// useRouter
import { useRouter } from 'next/navigation'
 
const router = useRouter()
 
router.push('/dashboard', { scroll: false })
```

### Prefetching links in Middleware

인증(authentication)이나 사용자를 다른 페이지로 재작성(rewriting)하는 등의 목적으로 Middleware를 사용하는 경우가 많습니다. Middleware를 통해 재작성된 링크에 대해 `<Link />` 컴포넌트가 올바르게 prefetching하려면, Next.js에 표시할 URL과 prefetch할 URL을 모두 알려주어야 합니다. 이는 올바른 라우트를 prefetch하기 위해 middleware에 불필요한 fetch 요청이 발생하지 않도록 하기 위함입니다.

예를 들어, 인증된 사용자와 방문자에게 각각 다른 뷰를 제공하는 `/dashboard` 라우트를 제공하고자 한다면, 사용자를 올바른 페이지로 리다이렉트하기 위해 Middleware에 다음과 같이 코드를 추가할 수 있습니다:

middleware.ts

```tsx
import { NextResponse } from 'next/server'
 
export function middleware(request: Request) {
  const nextUrl = request.nextUrl
  if (nextUrl.pathname === '/dashboard') {
    if (request.cookies.authToken) {
      return NextResponse.rewrite(new URL('/auth/dashboard', request.url))
    } else {
      return NextResponse.rewrite(new URL('/public/dashboard', request.url))
    }
  }
}
```

이 경우, `<Link />` 컴포넌트에서 다음과 같이 코드를 사용합니다:

app/page.tsx

```tsx
'use client'
 
import Link from 'next/link'
import useIsAuthed from './hooks/useIsAuthed' // Your auth hook
 
export default function Page() {
  const isAuthed = useIsAuthed()
  const path = isAuthed ? '/auth/dashboard' : '/public/dashboard'
  return (
    <Link as="/dashboard" href={path}>
      Dashboard
    </Link>
  )
}
```

---

번역 과정에서 잘 이해되지 않거나 추가 설명이 필요한 부분이 있다면, 솔직하게 모르는 이유를 말씀드리겠습니다. 현재 제공된 공식문서 내용에 관해서는 제가 가진 지식을 바탕으로 최대한 자연스럽고 자세하게 번역하였으므로, 추가적인 생략 없이 원문 그대로의 내용을 한국어로 전달하였습니다.

혹시 더 궁금한 점이나 추가 번역이 필요한 내용이 있다면 언제든지 질문해 주세요. Next.js의 다른 개념이나 사용 방법에 대해서도 자세히 설명해 드릴 수 있습니다.

#nextJS #공식문서 