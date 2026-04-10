---
name: animations
description: Add professional, agency-quality animations to React/Next.js websites. Use when building hero sections with entrance effects, scroll-triggered animations, parallax depth effects, smooth page transitions, hover micro-interactions, staggered list reveals, text animations, or any motion design that makes a site feel premium and high-converting. Covers Framer Motion, GSAP + ScrollTrigger, Lenis smooth scroll, and CSS-only transitions.
argument-hint: "[animation type or component]"
license: MIT
metadata:
  author: custom
  version: "1.0.0"
---

# Animations Skill

Professional motion design for React/Next.js websites. Implements agency-quality animations using Framer Motion, GSAP, Lenis smooth scroll, and advanced CSS — the same stack used by top digital agencies.

## When to Use This Skill

Use when:
- Building hero sections that need cinematic entrance animations
- Adding scroll-triggered reveals (elements animate in as user scrolls)
- Creating parallax depth effects on backgrounds and images
- Implementing smooth scroll behaviour across the whole page
- Adding hover micro-interactions (scale, glow, border, magnetic effects)
- Building animated page/route transitions
- Creating staggered list or card reveal animations
- Animating text (character-by-character, word-by-word, typewriter)
- Making number/counter animations
- Matching premium agency aesthetics (wearebrand.io level)

## Core Stack

### Framer Motion — Primary Animation Library
- React-native, component-level animations
- `motion.div`, `AnimatePresence`, `useScroll`, `useTransform`
- Best for: entrance animations, hover effects, page transitions, stagger
- Install: `npm install framer-motion`

### GSAP + ScrollTrigger — Advanced Scroll Animations
- Industry standard for scroll-driven and timeline animations
- Best for: parallax, pinned sections, scrub animations, complex sequences
- Install: `npm install gsap`
- ScrollTrigger is included in GSAP

### Lenis — Smooth Scroll
- Replaces native scroll with buttery-smooth momentum scrolling
- Single file, integrates with GSAP or standalone
- Install: `npm install lenis`

### CSS Animations — Zero-dependency Effects
- Tailwind `transition`, `duration`, `ease`, `animate-*` utilities
- Custom keyframes for shimmer, pulse, float, spin
- Best for: simple hover states, loading skeletons, subtle loops

## Quick Start

### 1. Install dependencies
```bash
npm install framer-motion gsap lenis
```

### 2. Add Lenis smooth scroll (wrap your app)
```tsx
// app/providers.tsx
"use client"
import { useEffect } from "react"
import Lenis from "lenis"

export function SmoothScroll({ children }: { children: React.ReactNode }) {
  useEffect(() => {
    const lenis = new Lenis({
      duration: 1.2,
      easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
    })

    function raf(time: number) {
      lenis.raf(time)
      requestAnimationFrame(raf)
    }
    requestAnimationFrame(raf)

    return () => lenis.destroy()
  }, [])

  return <>{children}</>
}
```

### 3. Animate a section on scroll (Framer Motion)
```tsx
"use client"
import { motion } from "framer-motion"
import { useInView } from "framer-motion"
import { useRef } from "react"

export function AnimatedSection({ children }: { children: React.ReactNode }) {
  const ref = useRef(null)
  const isInView = useInView(ref, { once: true, margin: "-100px" })

  return (
    <motion.div
      ref={ref}
      initial={{ opacity: 0, y: 60 }}
      animate={isInView ? { opacity: 1, y: 0 } : {}}
      transition={{ duration: 0.8, ease: [0.25, 0.1, 0.25, 1] }}
    >
      {children}
    </motion.div>
  )
}
```

## Animation Principles

### Timing Guidelines
| Effect | Duration | Easing |
|--------|----------|--------|
| Micro-interactions (hover) | 150–250ms | ease-out |
| Entrance animations | 600–900ms | cubic-bezier(0.25, 0.1, 0.25, 1) |
| Page transitions | 400–600ms | ease-in-out |
| Stagger delay between items | 80–120ms | — |
| Parallax scrub | tied to scroll | linear |

### Easing Reference
```ts
// Premium easings
const easings = {
  smooth: [0.25, 0.1, 0.25, 1],       // Standard smooth
  snappy: [0.16, 1, 0.3, 1],           // Fast start, slow end (iOS feel)
  cinematic: [0.76, 0, 0.24, 1],       // Slow start, fast middle, slow end
  bounce: [0.34, 1.56, 0.64, 1],       // Slight overshoot
}
```

### Accessibility — Always Include
```tsx
// Respect reduced motion preference
import { useReducedMotion } from "framer-motion"

function AnimatedComponent() {
  const shouldReduce = useReducedMotion()
  return (
    <motion.div
      animate={{ opacity: 1, y: shouldReduce ? 0 : 0 }}
      initial={{ opacity: 0, y: shouldReduce ? 0 : 40 }}
    />
  )
}
```

## Reference Navigation

### Core References
- `references/framer-motion.md` — Component animations, stagger, useScroll, text effects
- `references/gsap-scrolltrigger.md` — Parallax, pinned sections, scrub timelines
- `references/hero-animations.md` — Full hero section patterns (agency-level)
- `references/scroll-animations.md` — Scroll reveals, counters, progress bars
- `references/micro-interactions.md` — Hover effects, magnetic buttons, cursor
- `references/page-transitions.md` — Route transitions, exit animations

## Common Patterns At a Glance

### Fade up on scroll
```tsx
<motion.div
  initial={{ opacity: 0, y: 50 }}
  whileInView={{ opacity: 1, y: 0 }}
  viewport={{ once: true, amount: 0.3 }}
  transition={{ duration: 0.7, ease: [0.25, 0.1, 0.25, 1] }}
>
  {content}
</motion.div>
```

### Staggered card reveal
```tsx
const container = {
  hidden: {},
  show: { transition: { staggerChildren: 0.1 } }
}
const item = {
  hidden: { opacity: 0, y: 40 },
  show: { opacity: 1, y: 0, transition: { duration: 0.6 } }
}

<motion.div variants={container} initial="hidden" whileInView="show" viewport={{ once: true }}>
  {cards.map((card) => (
    <motion.div key={card.id} variants={item}>{card.content}</motion.div>
  ))}
</motion.div>
```

### Scale on hover
```tsx
<motion.div
  whileHover={{ scale: 1.05 }}
  whileTap={{ scale: 0.97 }}
  transition={{ type: "spring", stiffness: 400, damping: 17 }}
>
  {content}
</motion.div>
```

### Parallax image (GSAP)
```tsx
useEffect(() => {
  gsap.to(imageRef.current, {
    yPercent: -20,
    ease: "none",
    scrollTrigger: {
      trigger: sectionRef.current,
      start: "top bottom",
      end: "bottom top",
      scrub: true,
    }
  })
}, [])
```

## Best Practices

1. **Always use `once: true`** on scroll animations — elements shouldn't re-animate on scroll back
2. **Use `will-change: transform`** via Tailwind `will-change-transform` on animated elements for GPU compositing
3. **Stagger duration = element count × 80ms** — keeps reveals under 800ms total for short lists
4. **Never animate layout properties** (width, height, padding) — only `transform` and `opacity` for 60fps
5. **Lenis first** — add smooth scroll before any other animation; it changes the feel of everything
6. **GSAP for scroll-scrub, Framer Motion for everything else** — don't mix scroll libraries
7. **Test on mobile** — reduce parallax intensity by 50% on touch devices
8. **Keep hero animations under 1.2s** — users shouldn't wait to see content
9. **Use `AnimatePresence`** for any element that conditionally mounts/unmounts
10. **Respect `prefers-reduced-motion`** — use `useReducedMotion()` hook

## Resources
- Framer Motion Docs: https://www.framer.com/motion/
- GSAP Docs: https://gsap.com/docs/v3/
- Lenis Docs: https://lenis.darkroom.engineering/
- GSAP ScrollTrigger: https://gsap.com/docs/v3/Plugins/ScrollTrigger/
- Easings visual reference: https://easings.net/
