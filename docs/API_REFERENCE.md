# Public API and Component Reference

This document describes all public pages, components, API routes, and configuration exposed by this Next.js application, along with examples and usage instructions.

- Repository name: `actions-learning-pathway`
- Framework: Next.js 13.5
- Runtime: React 18

## Table of Contents

- Overview
- Getting Started
- Public Pages and Components
  - App (`pages/_app.js`)
  - Document (`pages/_document.js`)
  - Home (`pages/index.js`)
- API Routes
  - GET `/api/hello`
- Configuration
  - `next.config.js`
  - `jsconfig.json` path aliases
- Styles
- Examples

## Overview

This app is a minimal Next.js site intended for learning GitHub Actions and GitHub Pages deployments. It includes:

- A custom App component for global styles
- A custom Document for HTML structure
- A single Home page at `/`
- A demo API route at `/api/hello`
- A Next.js configuration tuned for static export deployments (e.g., GitHub Pages)

## Getting Started

- Install dependencies
```bash
npm ci
```

- Run development server
```bash
npm run dev
# Visit http://localhost:3000
```

- Build the site (static export)
```bash
npm run build
# If you need a static folder for static hosting, also run:
npx next export
# Outputs to ./out
```

- Preview a static export locally (one option)
```bash
npx serve out
```

## Public Pages and Components

### App (`pages/_app.js`)

Signature:
```ts
export default function App(params: { Component: React.ComponentType; pageProps: Record<string, unknown> }): JSX.Element
```

Description:
- The root component that wraps every page
- Applies global CSS from `styles/globals.css`
- Receives the active `Component` and its `pageProps`

Typical customizations:
- Add global providers (state, theme, i18n)
- Inject a global layout around all pages

Example: Adding a layout and provider
```jsx
// pages/_app.js
import '@/styles/globals.css'
import { Fragment } from 'react'

function Layout({ children }) {
  return <div className="container">{children}</div>
}

export default function App({ Component, pageProps }) {
  return (
    <Fragment>
      <Layout>
        <Component {...pageProps} />
      </Layout>
    </Fragment>
  )
}
```

### Document (`pages/_document.js`)

Signature:
```ts
export default function Document(): JSX.Element
```

Description:
- Controls the overall HTML document structure
- Sets `<Html lang="en">`, mounts `<Head/>`, `<Main/>`, and `<NextScript/>`

Typical customizations:
- Add meta tags, preload fonts, set `lang`, add analytics script tags

Example: Adding a meta tag and preloading a font
```jsx
// pages/_document.js
import { Html, Head, Main, NextScript } from 'next/document'

export default function Document() {
  return (
    <Html lang="en">
      <Head>
        <meta name="theme-color" content="#000000" />
        <link rel="preload" href="/fonts/Inter-Variable.woff2" as="font" type="font/woff2" crossOrigin="anonymous" />
      </Head>
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```

### Home (`pages/index.js`)

Signature:
```ts
export default function Home(): JSX.Element
```

Route: `/`

Description:
- A simple landing page using Next Head and the Google `Inter` font via `next/font/google`
- Uses a CSS module at `styles/Home.module.css`

Example: Updating the hero content
```jsx
// pages/index.js
import Head from 'next/head'
import { Inter } from 'next/font/google'
import styles from '@/styles/Home.module.css'

const inter = Inter({ subsets: ['latin'] })

export default function Home() {
  return (
    <>
      <Head>
        <title>My Site</title>
        <meta name="description" content="My awesome site" />
      </Head>
      <main className={`${styles.main} ${inter.className}`}>
        <h1>Hello, world!</h1>
        <p>Welcome to my site.</p>
      </main>
    </>
  )
}
```

## API Routes

### GET `/api/hello`

File: `pages/api/hello.js`

Handler signature:
```ts
export default function handler(req: import('http').IncomingMessage & { query?: Record<string, string> }, res: import('http').ServerResponse): void
```

Description:
- Responds with a simple JSON payload
- Intended as a demo endpoint for learning workflows

Response (200 OK):
```json
{ "name": "John Doe" }
```

Example requests:
```bash
# cURL
curl -s http://localhost:3000/api/hello | jq .
```

```js
// Browser/Node (fetch)
const response = await fetch('/api/hello')
const data = await response.json()
console.log(data) // { name: 'John Doe' }
```

Extending the handler (example: greet by name):
```js
// pages/api/hello.js
export default function handler(req, res) {
  const { name } = req.query || {}
  res.status(200).json({ greeting: `Hello${name ? `, ${name}` : ''}!` })
}
// GET /api/hello?name=Jane -> { "greeting": "Hello, Jane!" }
```

## Configuration

### `next.config.js`

Current config:
```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'export',
  images: {
    unoptimized: true,
  },
  reactStrictMode: true,
}

module.exports = nextConfig
```

Key points:
- `output: 'export'`: opts into static HTML export suitable for static hosting (e.g., GitHub Pages)
- `images.unoptimized: true`: allows `next/image` in static export mode without additional image optimization tooling
- `reactStrictMode: true`: enables additional React warnings during development

Build and export:
```bash
npm run build
npx next export # generates ./out for static hosting
```

### `jsconfig.json` path aliases

```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./*"]
    }
  }
}
```

Usage example:
```js
// Import from project root using the @ alias
import styles from '@/styles/Home.module.css'
```

## Styles

- Global styles: `styles/globals.css` (loaded once in `pages/_app.js`)
- CSS Modules: `styles/Home.module.css`, imported per-component/page

Usage example:
```jsx
import styles from '@/styles/Home.module.css'

export default function Example() {
  return <div className={styles.main}>Styled content</div>
}
```

## Examples

- Run locally
```bash
npm ci && npm run dev
```

- Build and export static site
```bash
npm run build && npx next export
ls -la out
```

- Fetch the API from a page
```jsx
import { useEffect, useState } from 'react'

export default function ApiDemo() {
  const [data, setData] = useState(null)
  useEffect(() => {
    fetch('/api/hello').then(r => r.json()).then(setData)
  }, [])
  return <pre>{JSON.stringify(data, null, 2)}</pre>
}
```

---

If you add more pages, components, or API routes, follow the same structure above: document the route or export, list its inputs/outputs, and include a minimal usage example.
