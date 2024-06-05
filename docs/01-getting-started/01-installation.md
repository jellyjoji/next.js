---
title: Installation
description: Create a new Next.js application with `create-next-app`. Set up TypeScript, styles, and configure your `next.config.js` file.
related:
  title: Next Steps
  description: Learn about the files and folders in your Next.js project.
  links:
    - getting-started/project-structure
---

System Requirements:

- [Node.js 18.17](https://nodejs.org/) or later.
- macOS, Windows (including WSL), and Linux are supported.


설치전 요구사항 :
- node.js 18.17 버전 이상

## Automatic Installation
We recommend starting a new Next.js app using [`create-next-app`](/docs/app/api-reference/create-next-app), which sets up everything automatically for you. To create a project, run:

```bash filename="Terminal"
npx create-next-app@latest
```
위 명령어로 자동 설치할수있다.

On installation, you'll see the following prompts:

```txt filename="Terminal"
What is your project named? my-app
Would you like to use TypeScript? No / Yes
Would you like to use ESLint? No / Yes
Would you like to use Tailwind CSS? No / Yes
Would you like to use `src/` directory? No / Yes
Would you like to use App Router? (recommended) No / Yes
Would you like to use Turbopack for `next dev`?  No / Yes
Would you like to customize the default import alias (@/*)? No / Yes
What import alias would you like configured? @/*
```

After the prompts, `create-next-app` will create a folder with your project name and install the required dependencies.

If you're new to Next.js, see the [project structure](/docs/getting-started/project-structure) docs for an overview of all the possible files and folders in your application.

> **Good to know**:
>
> - Next.js now ships with [TypeScript](/docs/app/building-your-application/configuring/typescript), [ESLint](/docs/app/building-your-application/configuring/eslint), and [Tailwind CSS](/docs/app/building-your-application/styling/tailwind-css) configuration by default.
> - You can optionally use a [`src` directory](/docs/app/building-your-application/configuring/src-directory) in the root of your project to separate your application's code from configuration files.

## Manual Installation

To manually create a new Next.js app, install the required packages:

```bash filename="Terminal"
npm install next@latest react@latest react-dom@latest
```
정석적인 설치를 위해 위 패키기들을 설치해준다.

Open your `package.json` file and add the following `scripts`:
`package.json` 에서 아래 명령어들을 보고 사용한다.

```json filename="package.json"
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  }
}
```

These scripts refer to the different stages of developing an application:

- `dev`: runs [`next dev`](/docs/app/api-reference/next-cli#development) to start Next.js in development mode.
- `build`: runs [`next build`](/docs/app/api-reference/next-cli#build) to build the application for production usage.
- `start`: runs [`next start`](/docs/app/api-reference/next-cli#production) to start a Next.js production server.
- `lint`: runs [`next lint`](/docs/app/api-reference/next-cli#lint) to set up Next.js' built-in ESLint configuration.

### Creating directories

Next.js uses file-system routing, which means the routes in your application are determined by how you structure your files.

#### The `app` directory

For new applications, we recommend using the [App Router](/docs/app). This router allows you to use React's latest features and is an evolution of the [Pages Router](/docs/pages) based on community feedback.

Router 사용이 권장된다. 

Create an `app/` folder, then add a `layout.tsx` and `page.tsx` file. These will be rendered when the user visits the root of your application (`/`).

`layout.tsx` and `page.tsx` 는 root 에 표시된다.

<Image
  alt="App Folder Structure"
  srcLight="/docs/light/app-getting-started.png"
  srcDark="/docs/dark/app-getting-started.png"
  width="1600"
  height="363"
/>

Create a [root layout](/docs/app/building-your-application/routing/layouts-and-templates#root-layout-required) inside `app/layout.tsx` with the required `<html>` and `<body>` tags:

```tsx filename="app/layout.tsx" switcher
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

```jsx filename="app/layout.js" switcher
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```

Finally, create a home page `app/page.tsx` with some initial content:

```tsx filename="app/page.tsx" switcher
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
```

```jsx filename="app/page.js" switcher
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
```

> **Good to know**: If you forget to create `layout.tsx`, Next.js will automatically create this file when running the development server with `next dev`.

Learn more about [using the App Router](/docs/app/building-your-application/routing/defining-routes).

#### The `pages` directory (optional)

If you prefer to use the Pages Router instead of the App Router, you can create a `pages/` directory at the root of your project.

만약 App Router 대신 Page Router 를 사용하고 싶다면 pages/ 폴더를 root 로 잡으면 된다. 

Then, add an `index.tsx` file inside your `pages` folder. This will be your home page (`/`):

그리고 pages/ 에 index.tsx 를 추가하면 home page (/) 가 될것이다.

```tsx filename="pages/index.tsx" switcher
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
```

Next, add an `_app.tsx` file inside `pages/` to define the global layout. Learn more about the [custom App file](/docs/pages/building-your-application/routing/custom-app).

```tsx filename="pages/_app.tsx" switcher
// 타입스크립트
import type { AppProps } from 'next/app'

export default function App({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />
}
```

```jsx filename="pages/_app.js" switcher
// 자바스크립트
export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

Finally, add a `_document.tsx` file inside `pages/` to control the initial response from the server. Learn more about the [custom Document file](/docs/pages/building-your-application/routing/custom-document).

그리고 pages/ 에 _document_.tsx 를 추가하면 된다.


```tsx filename="pages/_document.tsx" switcher
import { Html, Head, Main, NextScript } from 'next/document'

export default function Document() {
  return (
    <Html>
      <Head />
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```


# Q. App router 와 page router 차이?
## App directory
app 폴더 하위에 모든 파일을 추가할 수 있다.
폴더명으로 경로를 정의하며, 폴더 안의 page.js 또는 route.js로 작성된 파일만 경로로써 사용할 수 있다.
```jsx
app/components/button.js -> /components/button (X)
app/dashboard/page.js -> /dashboard (O)
app/dashboard/nav.js -> /dashboard/nav (X)
```
`layout.tsx`
RootLayout
최상위 레이아웃으로 모든 페이지에서 공유됨
루트 레이아웃에서만<html>, <body> 태그를 포함
app/layout.js는 pages/_app.js, pages/_document.js 파일을 대체

`page.tsx`
웹 애플리케이션의 시작점이자, 루트(/) 경로 페이지

## Pages directory
pages 폴더 하위의 모든 폴더/파일명을 기반으로 경로로써 사용할 수 있다.

```jsx
pages/index.tsx → /
pages/about.tsx → /about
pages/content/index.tsx → /content
```

`index.tsx`
웹 애플리케이션의 시작점이자, 루트(/) 경로 페이지

`_app.tsx`
공통 레이아웃과 전역 상태 관리 페이지
프로젝트 최상위 컴포넌트 역할

`_documents.tsx`
SSR 시 사용되며, 초기 HTML과 CSS 구조를 정의
`<html>`, `<body>` 등 전체 페이지의 구조와 메타 태그, 외부 스크립트, 글꼴 등을 설정


Learn more about [using the Pages Router](/docs/pages/building-your-application/routing/pages-and-layouts).

> **Good to know**: Although you can use both routers in the same project, routes in `app` will be prioritized over `pages`. We recommend using only one router in your new project to avoid confusion.

#### The `public` folder (optional)

Create a `public` folder to store static assets such as images, fonts, etc. Files inside `public` directory can then be referenced by your code starting from the base URL (`/`).

`선택적인 public 폴더 사용`
이미지나 폰트같은 정적인 요소를 담아두기위해 public 폴더를 사용한다.
`/` 만으로 진입할수있다. 

## Run the Development Server

1. Run `npm run dev` to start the development server.
2. Visit `http://localhost:3000` to view your application.
3. Edit `app/page.tsx` (or `pages/index.tsx`) file and save it to see the updated result in your browser.

# 구동하기

1. 설치 후 터미널에 `npm run dev` 
2. `http://localhost:3000` 주소로 이동
3. 이제 `page.tsx` 에서 작업하면 된다.