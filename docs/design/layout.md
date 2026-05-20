# Spacing & Layout

## Page structure

```tsx
<main className="min-h-screen">
  <Hero />
  <Services />
  <About />
  <Team />
  <Contact />
</main>
```

## Section wrapper

```tsx
<section className="py-20 px-4">
  <div className="container mx-auto max-w-6xl">
    {/* content */}
  </div>
</section>
```

## Grid patterns

| Pattern | Class |
|---------|-------|
| 4-column features | `grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6` |
| 3-column cards | `grid grid-cols-1 md:grid-cols-3 gap-8` |
| 2-column split | `grid grid-cols-1 lg:grid-cols-2 gap-12 items-center` |

## Spacing scale

- Section padding: `py-20` (80px)
- Between heading and content: `mb-12`
- Card internal: `p-6`
- Stack of items: `space-y-4` or `gap-6`
