---
name: astro-repo-architect
description: Authoritative guide for scaffolding, organizing, and maintaining an enterprise-grade Astro project architecture. Enforces separation of concerns, file-based routing protocols, and type-safe content management.
---

# Astro Repository Architect

## 🎯 Primary Directive
Use this skill to scaffold new Astro applications, determine the correct placement of files, and enforce architectural best practices. You must prioritize TypeScript strictness, proper routing paradigms, and Astro's Island Architecture.

## 🏗️ Enterprise Directory Structure

Enforce the following project topology. Do not deviate without explicit user instruction.

\`\`\`text
my-astro-project/
├── public/                 # Untouched static assets (favicon.svg, robots.txt, manifests)
├── src/
│   ├── actions/            # Backend functions/RPCs (Astro Actions)
│   ├── assets/             # Processed assets (optimized images, global CSS/SCSS)
│   ├── components/         # Reusable UI (Grouped by domain: e.g., /ui, /forms, /layout)
│   ├── content/            # Type-safe Markdown/MDX + config.ts (Zod schemas)
│   ├── layouts/            # Shared page wrappers and document shells
│   ├── pages/              # File-based routing (.astro, .md) and API routes (.ts)
│   ├── utils/              # Pure functions, formatters, and shared business logic
│   ├── env.d.ts            # Critical TypeScript declarations for Astro/Vite
│   └── middleware.ts       # Edge-level request interception (Auth, redirects)
├── tests/                  # Unit and E2E tests (Playwright/Vitest)
├── .env                    # Local environment variables
├── astro.config.mjs        # Framework integrations and build adapters
├── package.json            # Dependencies and standard scripts
└── tsconfig.json           # Strict TypeScript configuration
\`\`\`

## 🧠 Core Architectural Rules

### 1. The Routing Layer (`src/pages/` & `src/middleware.ts`)
- **Strict Adherence:** Only place files in `src/pages/` that are intended to be publicly addressable routes.
- **API Endpoints:** Build server-side endpoints as `.ts` files exporting standard HTTP methods (e.g., `export const GET = ...`).
- **Middleware:** Use `src/middleware.ts` for global request interception (authentication checks, header manipulation) rather than duplicating logic inside page components.

### 2. The Content Layer (`src/content/`)
- **Type Safety First:** All content collections (Markdown, MDX, JSON) must be defined in `src/content/config.ts` using `astro:content` and Zod.
- **Agent Guardrail:** If asked to generate a blog or documentation site, automatically initialize the `config.ts` schema before writing the markdown files.

### 3. The Presentation Layer (`src/components/` & `src/layouts/`)
- **Islands Architecture:** Astro is zero-JS by default. When placing UI framework components (React, Vue, Svelte) in `src/components/`, assume they will render as static HTML. 
- **Hydration Directives:** You must explicitly instruct the inclusion of client directives (e.g., `client:load`, `client:visible`) in the parent page only when interactivity is strictly required.

### 4. Asset Pipeline (`src/assets/` vs `public/`)
- **Dynamic Optimization:** Place images in `src/assets/` to utilize Astro's built-in `<Image />` component for automatic WebP conversion and responsive resizing.
- **Static Passthrough:** Place highly specific, unhashed files (like `robots.txt` or absolute URL social images) in `public/`.

## ⚙️ Execution Steps for Agents

1. **Context Verification:** Locate `astro.config.mjs` to confirm the project root and identify installed integrations (e.g., Tailwind, React) before suggesting file placements.
2. **Scaffold with Types:** When creating the `src/` directory, ensure `env.d.ts` is present with `/// <reference path="../.astro/types.d.ts" />` to prevent TypeScript errors in the IDE.
3. **Action over API:** For client-to-server mutations (like form submissions), default to scaffolding Astro Actions in `src/actions/` rather than traditional API endpoints in `src/pages/api/`.