# Typography

## Font

Use Geist or Inter via `next/font/google`:

```tsx
import { Inter } from "next/font/google";
const inter = Inter({ subsets: ["latin"], variable: "--font-sans" });
```

Apply on body: `className={inter.variable + " font-sans antialiased"}`.

## Scale

| Use | Class | Size |
|-----|-------|------|
| Hero headline | `text-5xl md:text-7xl font-bold tracking-tight` | 48–72px |
| Section heading | `text-3xl md:text-4xl font-bold` | 30–36px |
| Card title | `text-xl font-semibold` | 20px |
| Body | `text-base` | 16px |
| Muted / caption | `text-sm text-muted-foreground` | 14px |

## Gradient headings

```tsx
<h1 className="gradient-text text-6xl font-bold tracking-tight">
  AI Solutions for the Future
</h1>
```

Always pair `gradient-text` with an explicit font-size and `font-bold`.
