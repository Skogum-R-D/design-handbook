# Stack & Versions

Always use these exact versions. Do not upgrade without updating this doc.

## Core

| Package | Version | Notes |
|---------|---------|-------|
| `next` | `16.2.0` | App Router, Turbopack dev server |
| `react` | `^19.0.0` | **Not** the RC string `^19.0.0-rc-...` |
| `react-dom` | `^19.0.0` | Must match react |
| `typescript` | `^5.4.5` | |

## Styling

| Package | Version | Notes |
|---------|---------|-------|
| `tailwindcss` | `^3.4.0` | **v3 only** — v4 uses different CSS syntax, breaks everything |
| `autoprefixer` | `^10.4.19` | |
| `postcss` | `^8.4.38` | |
| `tailwindcss-animate` | `^1.0.7` | |
| `tailwind-merge` | `^2.3.0` | |
| `class-variance-authority` | `^0.7.0` | |
| `clsx` | `^2.1.1` | |

## Animation

| Package | Version | Notes |
|---------|---------|-------|
| `framer-motion` | `^11.3.28` | All components using this need `"use client"` |

## UI Primitives

| Package | Version | Notes |
|---------|---------|-------|
| `lucide-react` | `^0.468.0` | Must be >=0.468.0 for React 19 support |
| `@radix-ui/react-slot` | `^1.1.0` | |
| `next-themes` | `^0.3.0` | **Do not use** with React 19 — see Gotchas |

## package.json template

```json
{
  "dependencies": {
    "next": "16.2.0",
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "tailwindcss": "^3.4.0",
    "framer-motion": "^11.3.28",
    "lucide-react": "^0.468.0",
    "class-variance-authority": "^0.7.0",
    "clsx": "^2.1.1",
    "tailwind-merge": "^2.3.0",
    "tailwindcss-animate": "^1.0.7"
  },
  "devDependencies": {
    "@types/node": "^20.14.2",
    "@types/react": "^18.3.3",
    "@types/react-dom": "^18.3.0",
    "autoprefixer": "^10.4.19",
    "postcss": "^8.4.38",
    "typescript": "^5.4.5"
  }
}
```

## postcss.config.mjs

```js
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};
```

!!! warning
    Never use `@tailwindcss/postcss` — that is the Tailwind v4 plugin and is incompatible with v3 syntax.
