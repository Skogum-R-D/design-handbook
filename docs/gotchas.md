# Gotchas & Fixes

Every bug we've hit in production. Read this before debugging.

---

## 1. Tailwind v4 breaks everything — always use v3

**Symptom**: CSS not applied, `@tailwind base` directive throws PostCSS errors, `bg-background` class does nothing.

**Cause**: The generated code uses Tailwind v3 syntax. Tailwind v4 uses a completely different CSS import system (`@import "tailwindcss"`) and CSS-first config.

**Fix**: Pin to Tailwind v3 and use the standard PostCSS config:

```json
"tailwindcss": "^3.4.0"
```

```js
// postcss.config.mjs
export default {
  plugins: { tailwindcss: {}, autoprefixer: {} },
};
```

Never use `@tailwindcss/postcss` — that is the v4 plugin.

---

## 2. All framer-motion components need "use client"

**Symptom**: `Element type is invalid: expected a string... but got: undefined`

**Cause**: Next.js App Router renders components on the server by default. `framer-motion` is a client-only library. Without `"use client"`, `motion` is `undefined` at runtime.

**Fix**: Add `"use client"` as the **first line** of every file that imports from `framer-motion`:

```tsx
"use client";
import { motion } from "framer-motion";
```

This applies to: page sections, card components, button components — anything that animates.

---

## 3. next-themes is incompatible with React 19

**Symptom**: `Element type is invalid... but got: undefined` even after adding `"use client"` everywhere.

**Cause**: `next-themes@0.3.0` doesn't support React 19's new rendering model.

**Fix**: Replace the ThemeProvider with a passthrough and set dark mode via the `class` attribute directly on `<html>`:

```tsx
// components/theme-provider.tsx
"use client";
import * as React from "react";

interface ThemeProviderProps {
  children: React.ReactNode;
  [key: string]: unknown;
}

export function ThemeProvider({ children }: ThemeProviderProps) {
  return <>{children}</>;
}
```

```tsx
// app/layout.tsx
<html lang="en" className="dark" suppressHydrationWarning>
```

---

## 4. Turbopack is case-sensitive — filenames must match imports exactly

**Symptom**: `Module not found: Can't resolve '@/components/Hero'` even though `hero.tsx` exists.

**Cause**: Turbopack enforces case-sensitive path resolution even on macOS (which has a case-insensitive filesystem by default).

**Fix**: Ensure the filename on disk matches the import exactly:

```tsx
// Wrong:
import Hero from "@/components/Hero";  // file is hero.tsx

// Correct:
import Hero from "@/components/hero";  // matches hero.tsx
```

Or rename the file to `Hero.tsx` to match the import. Pick one convention and stick to it. We use **PascalCase filenames** for components.

---

## 5. Always use export default for page components

**Symptom**: `Export default doesn't exist in target module`

**Cause**: A component uses a named export (`export function Hero()`) but is imported as a default import (`import Hero from ...`).

**Fix**: Always use `export default` for top-level page components:

```tsx
// Wrong:
export function Hero() { ... }

// Correct:
export default function Hero() { ... }
```

Named exports are fine for utility components exported alongside others (e.g. `Card`, `CardHeader`).

---

## 6. Use react@^19.0.0 not the RC version string

**Symptom**: Peer dependency conflicts on `npm install`, especially with `lucide-react`.

**Cause**: The RC version string `^19.0.0-rc-f994737d14-20240522` doesn't satisfy `peer react: "^19.0.0"` in some packages.

**Fix**:

```json
"react": "^19.0.0",
"react-dom": "^19.0.0"
```

---

## 7. lucide-react must be >= 0.468.0 for React 19

**Symptom**: `ERESOLVE unable to resolve dependency tree` on `npm install`.

**Cause**: `lucide-react@0.378.0` declares `peer react: "^16.5.1 || ^17.0.0 || ^18.0.0"` — no React 19.

**Fix**:

```json
"lucide-react": "^0.468.0"
```

---

## 8. className must default to "" in forwardRef components

**Symptom**: Class list contains the literal string `"undefined"`, e.g. `class="p-6 undefined"`.

**Cause**: When `className` prop is not passed, it's `undefined`. Concatenating `"prefix " + undefined` gives `"prefix undefined"`.

**Fix**: Always destructure with a default:

```tsx
// Wrong:
({ className, ...props }, ref) => (
  <div className={"p-6 " + className} ...>

// Correct:
({ className = "", ...props }, ref) => (
  <div className={`p-6 ${className}`} ...>
```

---

## 9. Import paths must use @/ alias, not absolute /

**Symptom**: `Module not found: Can't resolve './components/theme-provider'` or similar.

**Cause**: An import uses an absolute path starting with `/` instead of the `@/` alias.

**Fix**:

```tsx
// Wrong:
import { ThemeProvider } from "/components/theme-provider";

// Correct:
import { ThemeProvider } from "@/components/theme-provider";
```

The `@/` alias is configured in `tsconfig.json` as `"paths": { "@/*": ["./*"] }`.

---

## 10. Dark theme CSS variables — foreground must be light

**Symptom**: Dark background but black text — invisible.

**Cause**: The `--foreground` variable was set to a near-black value (`222.2 84% 4.9%`) in both `:root` and `.dark`, making text invisible on dark backgrounds.

**Fix**: `--foreground` must be a **light** value in dark mode:

```css
:root {
  --background: 222 47% 5%;   /* very dark */
  --foreground: 210 40% 98%;  /* very light */
}
```

See [Colors & Themes](design/colors.md) for the full correct variable set.


---

## Gotcha 11 — Agents add packages but never update the lock file

**Symptom:** `Module not found: Can't resolve 'resend'` (or any other package) in Vercel/CI even though the package is listed in `package.json`.

**Root cause:** Agents push `package.json` edits via the GitHub API — they never run `npm install`. The `package-lock.json` is never updated, so CI/Vercel can't resolve the package from the lock file.

**Fix:** After any agent adds a package, run locally:
```bash
cd <repo>
npm install --legacy-peer-deps
git add package-lock.json
git commit -m "chore: update package-lock.json"
git push origin main
```

**Prevention:** Always commit `package-lock.json` to the repo. If it's missing entirely, run `npm install --legacy-peer-deps` once and push it.

---

## Gotcha 12 — Agents over-engineer API routes with uninstalled packages

**Symptom:** Build errors for `@upstash/ratelimit`, `@upstash/redis`, `dompurify`, `jsdom` — packages the agent added without being asked.

**Root cause:** The model sometimes adds rate limiting, sanitization, and other "nice to have" extras beyond the assignment scope, importing packages that aren't installed.

**Fix:** Rewrite the route to only import what's in `package.json`. For the contact form, the only allowed imports are `next/server` and `resend`.

**Prevention:** Assignment descriptions should explicitly say: *"Do not add any packages beyond what is already installed. Keep the implementation minimal."*

---

## Gotcha 13 — Python `def` syntax in TypeScript files

**Symptom:** `Expected ';', '}' or <eof>` build error on a line like `def getRatelimit() {`.

**Root cause:** The model occasionally slips into Python syntax when writing TypeScript, especially for helper functions.

**Fix:** Replace `def functionName()` with `function functionName()`.

**Prevention:** QA static analysis catches this — ensure QA always reads `route.ts` and component files before approving.


---

## Gotcha 14 — Wrong import path for UI components

**Symptom:** `Uncaught Error: ./components/navbar.tsx:6:1` or similar — component resolves to `undefined` at runtime.

**Root cause:** Agents import from `@/components/button` or `@/components/card` instead of `@/components/ui/button` and `@/components/ui/card`. The `ui/` subdirectory is required.

**Fix:** Change all shadcn/ui component imports:
```ts
// Wrong
import { Button } from "@/components/button";
import { Card } from "@/components/card";

// Correct
import { Button } from "@/components/ui/button";
import { Card, CardHeader, CardTitle, CardContent } from "@/components/ui/card";
```

**Prevention:** The system prompt already says `import from "@/components/ui/..."` — reinforce this in every task description that involves UI components.
