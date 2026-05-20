# Glassmorphism

The `.glassmorphism` utility class is defined in `globals.css`:

```css
.glassmorphism {
  background: rgba(15, 23, 42, 0.6);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.08);
}
```

## Usage

Apply to any card or panel container:

```tsx
<div className="glassmorphism rounded-xl p-6">
  {/* content */}
</div>
```

## Hover effect

Use Framer Motion's `whileHover` for a lift effect:

```tsx
<motion.div
  className="glassmorphism rounded-xl p-6"
  whileHover={{ y: -4, boxShadow: "0 20px 40px -10px rgba(59,130,246,0.15)" }}
  transition={{ type: "spring", stiffness: 300, damping: 20 }}
>
```

## Rules

- Always pair with a dark background — glassmorphism needs depth behind it
- Use `rounded-xl` (12px) as the standard border radius
- Keep `border-opacity` low (0.08–0.12) — subtle is better
- Do not stack multiple glassmorphism layers
