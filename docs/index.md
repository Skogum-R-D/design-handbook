# Skogum R&D Design Handbook

This handbook is the single source of truth for all frontend work at Skogum R&D. The frontend agent reads these docs before generating any code.

## What's in here

- **Stack & Versions** — exact pinned versions of every package. Never deviate.
- **Design System** — colors, typography, spacing, glassmorphism, animations
- **Components** — working reference implementations for Button, Card, Input
- **Gotchas & Fixes** — every bug we've hit and how to fix it. Read this before debugging.

## Core principles

- Dark theme by default — `class="dark"` on `<html>`
- Glassmorphism cards with `backdrop-filter: blur`
- Framer Motion for all animations — entrance, hover, stagger
- Mobile-first responsive layout
- No placeholder images — use CSS gradients or SVG
- Every component that uses framer-motion must have `"use client"` at the top
