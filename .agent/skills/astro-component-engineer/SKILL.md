---
name: astro-component-engineer
description: Authoritative protocol for writing, structuring, and optimizing .astro components. Enforces strict TypeScript interfaces, zero-JS templating, server-side data fetching, and explicit UI framework hydration (Islands Architecture).
---

# Astro Component Engineer

## 🎯 Primary Directive
Use this skill when generating or modifying `.astro` files. You must treat the `.astro` file as a server-side templating engine, not a client-side application. Enforce strict TypeScript types for all component props and require explicit client directives when integrating interactive UI frameworks.

## 🧬 Anatomy of an Astro Component

Every `.astro` component is strictly divided into two operational zones: the **Component Script** and the **Component Template**. Do not bleed client logic into the Component Script.

### 1. The Component Script (Server-Side Context)
- **Execution:** Runs **only** on the server at build time (or request time in SSR). It never ships to the browser.
- **Syntax:** Enclosed in a `---` code fence (frontmatter).
- **Mandates:**
  - Define strict TypeScript interfaces for `Props`.
  - Perform all secure data fetching (database queries, external APIs) here.
  - Import other components (Astro, React, Vue) here.
  - Read environment variables here.

\`\`\`astro
---
// Component Script Zone (Server-Side Only)
import { getCollection } from 'astro:content';
import InteractiveReactButton from './InteractiveReactButton.tsx';

interface Props {
  title: string;
  isDraft?: boolean;
}

const { title, isDraft = false } = Astro.props;
const posts = await getCollection('blog'); // Top-level await is native
---
\`\`\`

### 2. The Component Template (HTML/JSX Hybrid)
- **Execution:** Renders the HTML output.
- **Syntax:** JSX-like HTML below the code fence.
- **Mandates:**
  - Map over arrays using `.map()`.
  - Use standard HTML attributes (e.g., `class` instead of `className`).
  - No client-side state hooks (`useState`, `onMount`) can exist here.
  - Use `<style>` and `<script>` tags for raw CSS/JS scoped strictly to the component.

\`\`\`astro
<article class="prose lg:prose-xl">
  <h1>{title}</h1>
  {isDraft && <span class="badge">Draft</span>}
  
  <ul>
    {posts.map(post => <li>{post.data.title}</li>)}
  </ul>

  <style>
    h1 { color: var(--theme-text); }
  </style>

  <script>
    console.log("This runs in the browser!");
  </script>
</article>
\`\`\`

## 🏝️ Islands Architecture & Hydration

Astro is fundamentally zero-JS. When you import a React, Vue, or Svelte component into an `.astro` file, it will render as static HTML by default. 

To make an imported UI framework component interactive, you **must** apply a client directive.

- **`client:load`**: High priority. Hydrates immediately on page load. Use for critical UI (e.g., Navigation toggles, primary call-to-action).
- **`client:visible`**: Medium priority. Hydrates only when the component enters the viewport. Use for below-the-fold interactions (e.g., Image carousels, heavy charts).
- **`client:idle`**: Low priority. Hydrates when the main thread is free. Use for non-critical elements (e.g., tracking pixels, complex footers).
- **`client:only="react"`**: Skips server-side rendering entirely. Use for components that strictly depend on browser APIs (e.g., `window`, `localStorage`) on initial render.

\`\`\`astro
---
import ReactCarousel from '../components/ReactCarousel';
---
<ReactCarousel client:visible images={imageArray} />

<ReactCarousel images={imageArray} />
\`\`\`

## 🧩 Content Projection (Slots)

Use the `<slot />` element to pass child elements from a parent component into a child component.

- **Default Slot:** `<slot />` acts as the placeholder for arbitrary children.
- **Named Slots:** Use `name="header"` in the child layout, and `slot="header"` on the parent element injecting the content. Fallback content can be placed directly inside the `<slot>Fallback</slot>` tags.

## ⚙️ Execution Steps for Agents

1. **Verify Context:** Before writing a component, determine if it requires client-side state. If it does, write it in a UI framework (e.g., React `.tsx`) and import it into the `.astro` file with a hydration directive.
2. **Type the Props:** Always write an `interface Props` and explicitly destructure `Astro.props`.
3. **Format Checking:** Ensure `class` is used instead of `className` within `.astro` files, and ensure standard HTML event handlers (if using vanilla `<script>`) are handled properly without React-specific syntaxes like `onClick={}`.