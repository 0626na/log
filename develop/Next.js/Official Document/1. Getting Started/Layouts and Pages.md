> 이 글은 chat gpt로 번역한 Next.js 공식문서 입니다. 오직 개인의 학습을 위함이 목적입니다.
> 원문: [Layouts and Pages](https://nextjs.org/docs/app/getting-started/layouts-and-pages)

다음은 Next.js 공식문서 “How to create layouts and pages”의 내용을 전자책 형식으로 정리한 번역본입니다.  
이 번역본은 Next.js를 처음 배우는 개발자 분들이 이해하기 쉽도록 자연스러운 한국어로 자세하게 번역한 내용입니다.

---

# 레이아웃과 페이지 생성 방법(How to create layouts and pages)


Next.js는 **파일 시스템 기반 라우팅** 방식을 사용합니다. 즉, 폴더와 파일을 이용하여 라우트를 정의할 수 있습니다. 이 장에서는 레이아웃과 페이지를 생성하는 방법, 그리고 이들 간에 연결하는 방법에 대해 안내합니다.

---

## 페이지 생성(Creating a page)

**페이지**란 특정 라우트에서 렌더링되는 사용자 인터페이스(UI)를 의미합니다. 페이지를 생성하려면 `app` 디렉토리 안에 [[page.js|page 파일]]을 추가하고, 기본(default)으로 React 컴포넌트를 export 하면 됩니다. 예를 들어, 인덱스 페이지(`/`)를 생성하는 코드는 아래와 같습니다:

app/page.tsx
```tsx
export default function Page() {
  return <h1>Hello Next.js!</h1>
}
```

---

## 레이아웃 생성(Creating a layout)

레이아웃은 여러 페이지 간에 **공유되는** UI를 의미합니다. 페이지 간 이동 시 레이아웃은 상태를 유지하고 상호작용을 계속하며, 불필요한 재렌더링 없이 그대로 남아 있습니다.

레이아웃을 정의하려면 [[layout.js|layout 파일]]에서 기본적으로 React 컴포넌트를 export 합니다. 이 컴포넌트는 페이지 또는 다른 [[Layouts and Pages#레이아웃 중첩(Nesting layouts)|레이아웃]]을 포함할 수 있도록 `children` prop을 받아야 합니다.

예를 들어, 인덱스 페이지를 자식으로 받는 레이아웃을 생성하려면, `app` 디렉토리 안에 `layout` 파일을 추가하고 아래와 같이 작성합니다:

app/layout.tsx
```tsx
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>
        {/* 레이아웃 UI */}
        {/* 페이지 또는 중첩된 레이아웃을 렌더링할 위치에 children을 배치 */}
        <main>{children}</main>
      </body>
    </html>
  )
}
```

위 레이아웃은 `app` 디렉토리 최상위에 정의되었기 때문에 **루트 레이아웃**이라고 부릅니다. 루트 레이아웃은 **필수**이며 반드시 `html`과 `body` 태그를 포함해야 합니다.

---

## 중첩 라우트 생성(Creating a nested route)

중첩 라우트란 여러 개의 URL 세그먼트로 구성된 라우트를 말합니다. 예를 들어, `/blog/[slug]` 라우트는 다음과 같이 세 개의 세그먼트로 이루어져 있습니다:

- `/` (루트 세그먼트)
- `blog` (세그먼트)
- `[slug]` (리프(leaf) 세그먼트)

Next.js에서는 다음과 같이 처리합니다:

- **폴더**는 URL 세그먼트와 매핑되는 라우트 세그먼트를 정의하는 데 사용됩니다.
- **파일**(예: `page`, `layout`)은 해당 세그먼트에 표시될 UI를 생성하는 데 사용됩니다.

중첩 라우트를 생성하려면 폴더를 서로 중첩하면 됩니다. 예를 들어, `/blog` 라우트를 추가하려면 `app` 디렉토리 내에 `blog` 폴더를 생성하고, `/blog` 라우트를 공개적으로 접근할 수 있도록 `page` 파일을 추가합니다:

app/blog/page.tsx
```tsx
import { getPosts } from '@/lib/posts'
import { Post } from '@/ui/post'
 
export default async function Page() {
  const posts = await getPosts()
 
  return (
    <ul>
      {posts.map((post) => (
        <Post key={post.id} post={post} />
      ))}
    </ul>
  )
}
```

폴더를 계속 중첩하여 보다 세분화된 라우트를 생성할 수 있습니다. 예를 들어, 특정 블로그 포스트에 대한 라우트를 생성하려면, `blog` 폴더 내에 새로운 `[slug]` 폴더를 만들고 그 안에 `page` 파일을 추가합니다:

app/blog/\[slug\]/page.tsx
```tsx
function generateStaticParams() {}
 
export default function Page() {
  return <h1>Hello, Blog Post Page!</h1>
}
```

**참고**: 폴더 이름을 대괄호(`[]`)로 감싸면 (예: `[slug]`) 데이터로부터 여러 페이지를 생성하는 데 사용되는 특별한 [동적 라우트 세그먼트](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes)가 만들어집니다. 이는 블로그 포스트, 제품 페이지 등에서 매우 유용하게 사용됩니다.

---

## 레이아웃 중첩(Nesting layouts)

기본적으로 폴더 계층 구조 내의 레이아웃은 중첩되어, 부모 레이아웃이 자식 레이아웃을 `children` prop을 통해 감싸게 됩니다. 특정 라우트 세그먼트(폴더) 내에 `layout` 파일을 추가하여 레이아웃을 중첩할 수 있습니다.

예를 들어, `/blog` 라우트에 대한 레이아웃을 생성하려면 `blog` 폴더 내에 새로운 `layout` 파일을 추가합니다:

app/blog/layout.tsx
```tsx
export default function BlogLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return <section>{children}</section>
}
```

만약 위의 두 레이아웃(루트 레이아웃과 블로그 레이아웃)을 결합한다면, 루트 레이아웃(`app/layout.js`)이 블로그 레이아웃(`app/blog/layout.js`)을 감싸고, 블로그 페이지(`app/blog/page.js`)와 블로그 포스트 페이지(`app/blog/[slug]/page.js`)가 그 안에 포함되게 됩니다.

---

## 페이지 간 연결(Linking between pages)

라우트 간 이동을 위해 Next.js에서는 [`<Link>` 컴포넌트](https://nextjs.org/docs/app/api-reference/components/link)를 제공합니다. `<Link>` 컴포넌트는 HTML의 `<a>` 태그를 확장하여 프리페칭(prefetching) 및 클라이언트 사이드 네비게이션 기능을 추가한 내장 컴포넌트입니다.

예를 들어, 블로그 포스트 목록을 생성하려면 `next/link`에서 `<Link>`를 import 한 후, 컴포넌트에 `href` prop을 전달하는 방식으로 사용합니다:

app/ui/post.tsx
```tsx
import Link from 'next/link'
 
export default async function Post({ post }) {
  const posts = await getPosts()
 
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.slug}>
          <Link href={`/blog/${post.slug}`}>{post.title}</Link>
        </li>
      ))}
    </ul>
  )
}
```

`<Link>`는 Next.js 애플리케이션 내에서 라우트 간 이동을 위한 기본적이고 권장되는 방법입니다. 하지만 보다 고급의 네비게이션 기능이 필요하다면 [[useRouter|useRouter 훅]]을 사용할 수도 있습니다.

---

## API 레퍼런스

이 장에서 언급된 기능들에 대해 더 자세히 알고 싶다면, 아래의 API 레퍼런스를 참고하세요.
### [[layout.js]]
`layout.js` 파일에 대한 API 레퍼런스입니다.
### [[page.js]]
`page.js` 파일에 대한 API 레퍼런스입니다.
### [[Link]]
내장 `next/link` 컴포넌트를 사용하여 빠른 클라이언트 사이드 네비게이션을 구현하는 방법에 대해 설명합니다.

---

이상으로 Next.js의 레이아웃과 페이지 생성에 관한 공식문서 내용을 자세히 번역해 보았습니다. 혹시 이해하기 어려운 부분이나 추가적으로 궁금한 점이 있다면 언제든 질문해 주세요.  
(저는 Next.js 분야의 최고 전문가 '지피'입니다. 제가 모르는 내용은 없습니다!)


#nextJS #공식문서 