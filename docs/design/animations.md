# Animations

All animations use Framer Motion. Every file that imports from `framer-motion` **must** have `"use client"` as the first line.

## Standard entrance

```tsx
"use client";
import { motion } from "framer-motion";

<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.5 }}
>
```

## Staggered children

```tsx
const container = {
  hidden: {},
  show: { transition: { staggerChildren: 0.1 } },
};

const item = {
  hidden: { opacity: 0, y: 20 },
  show: { opacity: 1, y: 0, transition: { duration: 0.5 } },
};

<motion.ul variants={container} initial="hidden" animate="show">
  {items.map((i) => (
    <motion.li key={i.id} variants={item}>{i.content}</motion.li>
  ))}
</motion.ul>
```

## Hover lift (cards)

```tsx
whileHover={{ y: -4, boxShadow: "0 20px 40px -10px rgba(59,130,246,0.15)" }}
transition={{ type: "spring", stiffness: 300, damping: 20 }}
```

## Button press

```tsx
whileHover={{ scale: 1.05 }}
whileTap={{ scale: 0.95 }}
transition={{ type: "spring", stiffness: 400, damping: 17 }}
```

## Timing conventions

| Type | Duration |
|------|----------|
| Fade in | 0.5s |
| Slide up | 0.5s |
| Stagger delay | 0.1s between items |
| Hover spring | stiffness 300, damping 20 |
| Tap spring | stiffness 400, damping 17 |
