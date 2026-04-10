# Page Transitions Reference

Route-level transitions for Next.js App Router and Pages Router.

---

## Next.js App Router (Recommended)

### Layout-level fade transition

Wrap the page in a motion component inside the layout:

```tsx
// app/layout.tsx
import { PageTransitionWrapper } from "@/components/page-transition-wrapper"

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html>
      <body>
        <PageTransitionWrapper>{children}</PageTransitionWrapper>
      </body>
    </html>
  )
}
```

```tsx
// components/page-transition-wrapper.tsx
"use client"
import { motion, AnimatePresence } from "framer-motion"
import { usePathname } from "next/navigation"

export function PageTransitionWrapper({ children }: { children: React.ReactNode }) {
  const pathname = usePathname()

  return (
    <AnimatePresence mode="wait">
      <motion.div
        key={pathname}
        initial={{ opacity: 0, y: 20 }}
        animate={{ opacity: 1, y: 0 }}
        exit={{ opacity: 0, y: -20 }}
        transition={{ duration: 0.4, ease: [0.25, 0.1, 0.25, 1] }}
      >
        {children}
      </motion.div>
    </AnimatePresence>
  )
}
```

---

### Curtain wipe transition (agency style)

A black panel sweeps in and out between pages — premium, cinematic feel.

```tsx
// components/page-transition-curtain.tsx
"use client"
import { motion, AnimatePresence } from "framer-motion"
import { usePathname } from "next/navigation"

export function PageTransitionCurtain({ children }: { children: React.ReactNode }) {
  const pathname = usePathname()

  return (
    <>
      <AnimatePresence mode="wait">
        <motion.div key={pathname}>
          {/* Curtain overlay */}
          <motion.div
            className="fixed inset-0 z-50 bg-black pointer-events-none"
            initial={{ scaleY: 0, originY: "0%" }}
            animate={{
              scaleY: [0, 1, 1, 0],
              originY: ["0%", "0%", "100%", "100%"]
            }}
            transition={{
              duration: 0.9,
              times: [0, 0.45, 0.55, 1],
              ease: [0.76, 0, 0.24, 1]
            }}
          />
          {/* Page content */}
          <motion.div
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            transition={{ duration: 0.01, delay: 0.45 }}
          >
            {children}
          </motion.div>
        </motion.div>
      </AnimatePresence>
    </>
  )
}
```

---

### Slide between pages (directional)

Pages slide left/right based on nav direction. Good for multi-step flows.

```tsx
"use client"
import { motion, AnimatePresence } from "framer-motion"
import { usePathname } from "next/navigation"
import { useState, useEffect } from "react"

const navOrder = ["/", "/work", "/about", "/contact"]

export function SlideTransition({ children }: { children: React.ReactNode }) {
  const pathname = usePathname()
  const [direction, setDirection] = useState(1)
  const [prevPath, setPrevPath] = useState(pathname)

  useEffect(() => {
    const prevIndex = navOrder.indexOf(prevPath)
    const nextIndex = navOrder.indexOf(pathname)
    setDirection(nextIndex >= prevIndex ? 1 : -1)
    setPrevPath(pathname)
  }, [pathname])

  return (
    <AnimatePresence mode="wait" custom={direction}>
      <motion.div
        key={pathname}
        custom={direction}
        variants={{
          enter: (dir: number) => ({ x: dir * 60, opacity: 0 }),
          center: { x: 0, opacity: 1 },
          exit: (dir: number) => ({ x: dir * -60, opacity: 0 })
        }}
        initial="enter"
        animate="center"
        exit="exit"
        transition={{ duration: 0.45, ease: [0.25, 0.1, 0.25, 1] }}
      >
        {children}
      </motion.div>
    </AnimatePresence>
  )
}
```

---

## Individual Page Entrance Animations

Each page can define its own entrance without a shared wrapper.

### Simple page fade
```tsx
// app/about/page.tsx
"use client"
import { motion } from "framer-motion"

export default function AboutPage() {
  return (
    <motion.main
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      transition={{ duration: 0.5 }}
    >
      {/* page content */}
    </motion.main>
  )
}
```

### Staggered sections entrance per page
```tsx
export default function WorkPage() {
  return (
    <motion.main
      variants={{
        hidden: {},
        show: { transition: { staggerChildren: 0.15 } }
      }}
      initial="hidden"
      animate="show"
    >
      <motion.section variants={{ hidden: { opacity: 0, y: 40 }, show: { opacity: 1, y: 0 } }}>
        Hero
      </motion.section>
      <motion.section variants={{ hidden: { opacity: 0, y: 40 }, show: { opacity: 1, y: 0 } }}>
        Work Grid
      </motion.section>
      <motion.section variants={{ hidden: { opacity: 0, y: 40 }, show: { opacity: 1, y: 0 } }}>
        CTA
      </motion.section>
    </motion.main>
  )
}
```

---

## Preloader / First-Visit Intro

Plays once on first site load, then doesn't show again.

```tsx
"use client"
import { useEffect, useState } from "react"
import { motion, AnimatePresence } from "framer-motion"

export function Preloader() {
  const [show, setShow] = useState(true)

  useEffect(() => {
    const timer = setTimeout(() => setShow(false), 2200)
    return () => clearTimeout(timer)
  }, [])

  return (
    <AnimatePresence>
      {show && (
        <motion.div
          className="fixed inset-0 z-[200] bg-black flex items-center justify-center overflow-hidden"
          exit={{ clipPath: "inset(0 0 100% 0)" }}
          transition={{ duration: 0.8, ease: [0.76, 0, 0.24, 1], delay: 0.2 }}
        >
          {/* Brand name */}
          <motion.div
            initial={{ opacity: 0, y: 30 }}
            animate={{ opacity: 1, y: 0 }}
            transition={{ duration: 0.6, delay: 0.2 }}
            className="text-white text-4xl font-bold tracking-[0.3em] uppercase"
          >
            Your Brand
          </motion.div>

          {/* Loading bar */}
          <motion.div
            className="absolute bottom-0 left-0 h-1 bg-white origin-left"
            initial={{ scaleX: 0 }}
            animate={{ scaleX: 1 }}
            transition={{ duration: 1.8, ease: "easeInOut" }}
          />
        </motion.div>
      )}
    </AnimatePresence>
  )
}
```

---

## Transition Links (Animated navigation)

Make `<Link>` clicks trigger a transition before routing:

```tsx
"use client"
import { useRouter } from "next/navigation"
import { useCallback } from "react"

export function TransitionLink({
  href,
  children,
  className
}: {
  href: string
  children: React.ReactNode
  className?: string
}) {
  const router = useRouter()

  const handleClick = useCallback(
    async (e: React.MouseEvent) => {
      e.preventDefault()
      // Trigger exit animation
      document.documentElement.classList.add("page-transitioning")
      await new Promise((r) => setTimeout(r, 400)) // match exit duration
      router.push(href)
      document.documentElement.classList.remove("page-transitioning")
    },
    [href, router]
  )

  return (
    <a href={href} onClick={handleClick} className={className}>
      {children}
    </a>
  )
}
```

```css
/* globals.css */
.page-transitioning main {
  opacity: 0;
  transform: translateY(-20px);
  transition: opacity 0.4s ease, transform 0.4s ease;
}
```

---

## Best Practices for Page Transitions

1. **Keep transitions under 500ms** — any longer feels sluggish during navigation
2. **Use `mode="wait"` in AnimatePresence** — ensures exit completes before enter starts
3. **Don't animate layout-affecting properties** on page wrappers — only opacity and transform
4. **Add a loading state** for slow data-fetching pages so the transition doesn't hang
5. **Skip transitions on `prefers-reduced-motion`**:

```tsx
const prefersReduced = useReducedMotion()

<motion.div
  initial={{ opacity: prefersReduced ? 1 : 0 }}
  animate={{ opacity: 1 }}
  transition={{ duration: prefersReduced ? 0 : 0.4 }}
>
```
