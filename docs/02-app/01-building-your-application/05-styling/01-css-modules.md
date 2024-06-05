---
title: CSS Modules and Global Styles
nav_title: CSS Modules
description: Style your Next.js Application with CSS Modules, Global Styles, and external stylesheets.
---

{/_ The content of this doc is shared between the app and pages router. You can use the `<PagesOnly>Content</PagesOnly>` component to add content that is specific to the Pages Router. Any shared content should not be wrapped in a component. _/}

<PagesOnly>

<details open>
  <summary>Examples</summary>

- [Basic CSS Example](https://github.com/vercel/next.js/tree/canary/examples/basic-css)

</details>

</PagesOnly>

Next.js supports different types of stylesheets, including:

- [CSS Modules](#css-modules)
- [Example](#example)
- [Global Styles](#global-styles)
- [External Stylesheets](#external-stylesheets)
  - [Import styles from `node_modules`](#import-styles-from-node_modules)
- [Ordering and Merging](#ordering-and-merging)
- [Additional Features](#additional-features)

## CSS Modules

Next.js has built-in support for CSS Modules using the `.module.css` extension.

넥스트는 빌트인 CSS 모듈을 기본적으로 제공한다.

CSS Modules locally scope CSS by automatically creating a unique class name. This allows you to use the same class name in different files without worrying about collisions. This behavior makes CSS Modules the ideal way to include component-level CSS.

CSS 모듈은 유일한 클래스 이름을 생성함으로서 지역 범위 스코프로 작동한다.
따라서 다른 파일에서 충돌 걱정없이 같은 클래스 이름을 사용할수있도록 해준다.
이것이 컴포넌트에서 CSS 모듈을 사용하는 이유이다.

## Example

<AppOnly>
CSS Modules can be imported into any file inside the `app` directory:

CSS 모듈은 모든 파일에서 import 된다.

```tsx filename="app/dashboard/layout.tsx" switcher
import styles from "./styles.module.css";

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return <section className={styles.dashboard}>{children}</section>;
}
```

```jsx filename="app/dashboard/layout.js" switcher
import styles from "./styles.module.css";

export default function DashboardLayout({ children }) {
  return <section className={styles.dashboard}>{children}</section>;
}
```

```css filename="app/dashboard/styles.module.css"
.dashboard {
  padding: 24px;
}
```

</AppOnly>

<PagesOnly>

For example, consider a reusable `Button` component in the `components/` folder:

First, create `components/Button.module.css` with the following content:

```css filename="Button.module.css"
/*
You do not need to worry about .error {} colliding with any other `.css` or
`.module.css` files!
*/
.error {
  color: white;
  background-color: red;
}
```

Then, create `components/Button.js`, importing and using the above CSS file:

```jsx filename="components/Button.js"
import styles from "./Button.module.css";

export function Button() {
  return (
    <button
      type="button"
      // Note how the "error" class is accessed as a property on the imported
      // `styles` object.
      className={styles.error}
    >
      Destroy
    </button>
  );
}
```

</PagesOnly>

CSS Modules are **only enabled for files with the `.module.css` and `.module.sass` extensions**.

In production, all CSS Module files will be automatically concatenated into **many minified and code-split** `.css` files.
These `.css` files represent hot execution paths in your application, ensuring the minimal amount of CSS is loaded for your application to paint.

## Global Styles

<AppOnly>
Global styles can be imported into any layout, page, or component inside the `app` directory.

전역 스타일은 모든 레이아웃이나 컴포넌트에서 import 된다.

> **Good to know**: This is different from the `pages` directory, where you can only import global styles inside the `_app.js` file.

For example, consider a stylesheet named `app/global.css`:

```css
body {
  padding: 20px 20px 60px;
  max-width: 680px;
  margin: 0 auto;
}
```

Inside the root layout (`app/layout.js`), import the `global.css` stylesheet to apply the styles to every route in your application:

root layout 에서 전역 CSS 를 import 하면 모든 경로에 적용이 된다.

```tsx filename="app/layout.tsx" switcher
// These styles apply to every route in the application
import "./global.css";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

```jsx filename="app/layout.js" switcher
// These styles apply to every route in the application
import "./global.css";

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

</AppOnly>

<PagesOnly>

To add a stylesheet to your application, import the CSS file within `pages/_app.js`.

For example, consider the following stylesheet named `styles.css`:

```css filename="styles.css"
body {
  font-family: "SF Pro Text", "SF Pro Icons", "Helvetica Neue", "Helvetica",
    "Arial", sans-serif;
  padding: 20px 20px 60px;
  max-width: 680px;
  margin: 0 auto;
}
```

Create a [`pages/_app.js` file](/docs/pages/building-your-application/routing/custom-app) if not already present.
Then, [`import`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import) the `styles.css` file.

```jsx filename="pages/_app.js"
import "../styles.css";

// This default export is required in a new `pages/_app.js` file.
export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />;
}
```

These styles (`styles.css`) will apply to all pages and components in your application.
Due to the global nature of stylesheets, and to avoid conflicts, you may **only import them inside [`pages/_app.js`](/docs/pages/building-your-application/routing/custom-app)**.

In development, expressing stylesheets this way allows your styles to be hot reloaded as you edit them—meaning you can keep application state.

In production, all CSS files will be automatically concatenated into a single minified `.css` file. The order that the CSS is concatenated will match the order the CSS is imported into the `_app.js` file. Pay special attention to imported JS modules that include their own CSS; the JS module's CSS will be concatenated following the same ordering rules as imported CSS files. For example:

```jsx
import "../styles.css";
// The CSS in ErrorBoundary depends on the global CSS in styles.css,
// so we import it after styles.css.
import ErrorBoundary from "../components/ErrorBoundary";

export default function MyApp({ Component, pageProps }) {
  return (
    <ErrorBoundary>
      <Component {...pageProps} />
    </ErrorBoundary>
  );
}
```

</PagesOnly>

## External Stylesheets

<AppOnly>

Stylesheets published by external packages can be imported anywhere in the `app` directory, including colocated components:

부트스트랩 같은 이너털 패키지는 app 디랙토리 모든곳에서 import 된다.

```tsx filename="app/layout.tsx" switcher
import "bootstrap/dist/css/bootstrap.css";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className="container">{children}</body>
    </html>
  );
}
```

```jsx filename="app/layout.js" switcher
import "bootstrap/dist/css/bootstrap.css";

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body className="container">{children}</body>
    </html>
  );
}
```

> **Good to know**: External stylesheets must be directly imported from an npm package or downloaded and colocated with your codebase. You cannot use `<link rel="stylesheet" />`.

</AppOnly>

<PagesOnly>

Next.js allows you to import CSS files from a JavaScript file.
This is possible because Next.js extends the concept of [`import`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import) beyond JavaScript.

### Import styles from `node_modules`

Since Next.js **9.5.4**, importing a CSS file from `node_modules` is permitted anywhere in your application.

For global stylesheets, like `bootstrap` or `nprogress`, you should import the file inside `pages/_app.js`.
For example:

```jsx filename="pages/_app.js"
import "bootstrap/dist/css/bootstrap.css";

export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />;
}
```

For importing CSS required by a third-party component, you can do so in your component. For example:

```jsx filename="components/example-dialog.js"
import { useState } from "react";
import { Dialog } from "@reach/dialog";
import VisuallyHidden from "@reach/visually-hidden";
import "@reach/dialog/styles.css";

function ExampleDialog(props) {
  const [showDialog, setShowDialog] = useState(false);
  const open = () => setShowDialog(true);
  const close = () => setShowDialog(false);

  return (
    <div>
      <button onClick={open}>Open Dialog</button>
      <Dialog isOpen={showDialog} onDismiss={close}>
        <button className="close-button" onClick={close}>
          <VisuallyHidden>Close</VisuallyHidden>
          <span aria-hidden>×</span>
        </button>
        <p>Hello there. I am a dialog</p>
      </Dialog>
    </div>
  );
}
```

</PagesOnly>

<AppOnly>

## Ordering and Merging

순서와 병합

Next.js optimizes CSS during production builds by automatically chunking (merging) stylesheets. The CSS order is determined by the order in which you import the stylesheets into your application code.

css 스타일은 import 순서에 따라 결정된다.

For example, `base-button.module.css` will be ordered before `page.module.css` since `<BaseButton>` is imported first in `<Page>`:

예를 들면, base-bytton 이 page css 보다 먼저 import 되었기때문에 순서가 먼저일것이다.

```tsx filename="base-button.tsx" switcher
import styles from "./base-button.module.css";

export function BaseButton() {
  return <button className={styles.primary} />;
}
```

```jsx filename="base-button.js" switcher
import styles from "./base-button.module.css";

export function BaseButton() {
  return <button className={styles.primary} />;
}
```

```tsx filename="page.ts" switcher
import { BaseButton } from "./base-button";
import styles from "./page.module.css";

export function Page() {
  return <BaseButton className={styles.primary} />;
}
```

```jsx filename="page.js" switcher
import { BaseButton } from "./base-button";
import styles from "./page.module.css";

export function Page() {
  return <BaseButton className={styles.primary} />;
}
```

To maintain a predictable order, we recommend the following:

- Only import a CSS file in a single JS/TS file.
  - If using global class names, import the global styles in the same file in the order you want them to be applied.
- Prefer CSS Modules over global styles.
  - Use a consistent naming convention for your CSS modules. For example, using `<name>.module.css` over `<name>.tsx`.
- Extract shared styles into a separate shared component.
- If using [Tailwind](/docs/app/building-your-application/styling/tailwind-css), import the stylesheet at the top of the file, preferably in the [Root Layout](/docs/app/building-your-application/routing/layouts-and-templates#root-layout-required).

예측하기 쉬운 순서를 유지하기위해 아래와 같이 권장된다.

- 싱클 js 파일에서는 css 파일만 import 한다.
- 전역 스타일보다는 CSS 모듈을 사용해라ㅏ.
- 공유된 스타일만 추출해라.
- tailwind 를 사용한다면 스타일시트 가장 위에 파일을 import 해라. 혹은 아예 root layout 에 import 하는것이 권장된다.

> **Good to know:** CSS ordering behaves differently in development mode, always ensure to check preview deployments to verify the final CSS order in your production build.

</AppOnly>

## Additional Features

Next.js includes additional features to improve the authoring experience of adding styles:

- When running locally with `next dev`, local stylesheets (either global or CSS modules) will take advantage of [Fast Refresh](/docs/architecture/fast-refresh) to instantly reflect changes as edits are saved.
- When building for production with `next build`, CSS files will be bundled into fewer minified `.css` files to reduce the number of network requests needed to retrieve styles.
- If you disable JavaScript, styles will still be loaded in the production build (`next start`). However, JavaScript is still required for `next dev` to enable [Fast Refresh](/docs/architecture/fast-refresh).
