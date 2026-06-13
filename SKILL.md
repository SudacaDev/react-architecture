---
name: react-architecture
description: >
  Scaffold and enforce a scalable React project architecture with opinionated folder structure.
  Use this skill whenever the user wants to: create a new React/Next.js/Vite project structure...
version: "1.1.0"
author: "SudacaDev"
tags: ["react", "architecture", "nextjs", "vite"]
references:
  - "references/nextjs-app.md"
  - "references/nextjs-pages.md"
  - "references/vite.md"
  - "references/templates.md"
  - "references/patterns.md"
  - "references/graphify-usage.md"
---

# React Architecture Skill

Scaffold and maintain a **feature-based React architecture** across different stacks.

---

## Step 0 — Detect the Stack

Before generating anything, determine which stack the project uses.

### Detection (auto, no need to ask if obvious)

| Signal | Stack |
|--------|-------|
| `next.config.ts` / `next.config.js` exists, `app/` directory present | **Next.js App Router** |
| `next.config.*` exists, `pages/` directory present (no `app/`) | **Next.js Pages Router** |
| `vite.config.ts` / `vite.config.js` exists | **Vite + React Router** |
| Neither Next nor Vite config found | Ask the user |

### If you need to ask:

> "¿Qué stack estás usando? Next.js (App Router), Next.js (Pages Router), o Vite + React Router?"

---

## Core Philosophy (all stacks)

The project is always split into two layers:

| Layer | Responsibility |
|-------|----------------|
| **Route shell** | Thin wrapper — just imports and renders the feature. No logic. |
| **Feature** (`features/<name>-page/index.tsx`) | All real UI, state, hooks, sub-components |

> `features/` is the source of truth. Route shells are just entry points.

---

## Stack: Next.js App Router

Read `references/nextjs-app.md` for full details.

**Key differences:**
- Route shells live in `app/<route>/page.tsx` (Next.js convention, can't change)
- Features still live in `features/<route>-page/index.tsx`
- No `routes/` folder needed (file-system routing)
- Add `app/<route>/loading.tsx` and `error.tsx` when relevant
- Server Components by default — mark `"use client"` only when needed

**Structure:**
```
app/
├── layout.tsx
├── page.tsx                         ← home route shell
├── login/
│   ├── page.tsx                     ← imports LoginPage
│   └── loading.tsx                  ← optional
├── dashboard/
│   └── page.tsx
└── (auth)/                          ← route groups for layouts

src/
├── features/
│   ├── login-page/
│   │   ├── index.tsx
│   │   ├── components/
│   │   └── hooks/
│   └── dashboard-page/
│       └── index.tsx
├── components/ui/
├── hooks/
├── api/
├── services/
├── store/
├── utils/
└── assets/
```

---

## Stack: Next.js Pages Router

Read `references/nextjs-pages.md` for full details.

**Key differences:**
- Route shells live in `pages/<route>.tsx` or `pages/<route>/index.tsx`
- Special files: `pages/_app.tsx`, `pages/_document.tsx`
- API routes live in `pages/api/`
- No `routes/` folder needed

**Structure:**
```
pages/
├── _app.tsx
├── _document.tsx
├── index.tsx                        ← home
├── login.tsx                        ← imports LoginPage
├── dashboard.tsx
└── api/
    └── auth/
        └── [...nextauth].ts

src/
├── features/
│   ├── login-page/
│   │   ├── index.tsx
│   │   ├── components/
│   │   └── hooks/
│   └── dashboard-page/
│       └── index.tsx
├── components/ui/
├── hooks/
├── api/
├── services/
├── store/
├── utils/
└── assets/
```

---

## Stack: Vite + React Router

Read `references/vite.md` for full details.

**Key differences:**
- No file-system routing — routes are configured manually in `src/routes/`
- Entry point is `src/main.tsx`
- Route shells live in `src/pages/<name>/page.tsx`
- Full control over the router setup

**Structure:**
```
src/
├── main.tsx                         ← mounts the app
├── App.tsx                          ← router provider
├── pages/
│   ├── login/
│   │   └── page.tsx                 ← imports LoginPage
│   └── dashboard/
│       └── page.tsx
├── features/
│   ├── login-page/
│   │   ├── index.tsx
│   │   ├── components/
│   │   └── hooks/
│   └── dashboard-page/
│       └── index.tsx
├── routes/
│   ├── index.tsx                    ← createBrowserRouter config
│   └── ProtectedRoute.tsx
├── components/ui/
├── hooks/
├── api/
├── services/
├── store/
├── utils/
|── assets/
└── types/
```

---

## Naming Conventions (all stacks)

| Thing | Convention | Example |
|-------|-----------|---------|
| Route folder | kebab-case | `user-profile/` |
| Feature folder | `<route>-page` | `user-profile-page/` |
| Component export | PascalCase named export | `UserProfilePage` |
| Page shell export | default export | `export default function ...` |
| Hook file | `use-<name>.ts` | `use-auth.ts` |
| Hook export | camelCase | `useAuth` |
| API file | camelCase | `authApi.ts` |
| Service file | camelCase | `authService.ts` |

---

## What Goes Where (all stacks)

| Code type | Goes in |
|-----------|---------|
| Raw fetch/axios calls | `api/` |
| Token handling, error mapping, retry | `services/` |
| Component state / effects | Inside the feature |
| Reusable cross-feature hook | `hooks/` |
| Feature-specific hook | `features/<x>-page/hooks/` |
| Zustand/Redux store slice | `store/` |
| Button, Input, Card UI primitives | `components/ui/` |
| Date formatters, string utils | `utils/` |
| Router config, protected routes | `routes/` (Vite only) |
| Images, fonts, SVGs | `assets/` |

---

## Output Format

When scaffolding, always:
1. **State the detected stack** at the top ("Stack detectado: Next.js App Router")
2. Show the **folder tree** first
3. Show each **file's content** in order: route shell → feature → supporting files
4. Use `tsx` for components, `ts` for logic-only files
5. In Next.js App Router, add `"use client"` only when the component uses hooks/browser APIs

---

## Reference Files

Load the relevant file based on detected stack:

- `references/nextjs-app.md` — Next.js App Router: layouts, Server Components, loading/error, route groups
- `references/nextjs-pages.md` — Next.js Pages Router: _app, _document, getServerSideProps, API routes
- `references/vite.md` — Vite + React Router: main.tsx, createBrowserRouter, protected routes
- `references/templates.md` — Shared boilerplate: hooks, api, services, store, ui components
- `references/patterns.md` — Advanced: protected routes, lazy loading, barrel exports, error boundaries

## Configuración de Entorno y Knowledge Graph (Graphify)

Cuando inicialices, estructures o documentes el proyecto, es obligatorio configurar las reglas de contexto para herramientas de IA siguiendo estos pasos:

1. **Inyección en archivos de comportamiento:** En los archivos de configuración del espacio de trabajo como `CLAUDE.md`, `.cursorrules` y `.github/copilot-instructions.md` (crealos si no existen), debés asegurar que contengan las siguientes directivas:
   - En `CLAUDE.md`: Añadir la sección de instrucciones del proyecto apuntando a `.ai/behavior.md`, `.ai/role.md` y `.ai/skills/`.
   - En todos ellos, agregar la línea corta de manera explícita: 
     "Ver .ai/skills/graphify-usage/SKILL.md para el uso del knowledge graph del proyecto."

2. **Generación del Skill Local de Graphify:** Creá de forma exacta el archivo `.ai/skills/graphify-usage/SKILL.md` replicando el contenido plano especificado en el archivo de referencia `references/graphify-usage.md`.