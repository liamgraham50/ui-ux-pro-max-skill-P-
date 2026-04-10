# Framer Motion Reference

Complete patterns for component-level animations in React/Next.js using Framer Motion.

## Installation & Setup

```bash
npm install framer-motion
```

For Next.js App Router, mark animated components with `"use client"`.

---

## Core Concepts

### motion components
Any HTML element prefixed with `motion.` becomes animatable:
```tsx
import { motion } from "framer-motion"

<motion.div />
<motion.h1 />
<motion.img />
<motion.button />
// or for custom components:
const MotionCard = motion(Card)
```

### animate / initial / exit
```tsx
<motion.div
  initial={{ opacity: 0, y: 30 }}    // starting state
  animate={{ opacity: 1, y: 0 }}     // end state
  exit={{ opacity: 0, y: -30 }}      // when unmounted
  transition={{ duration: 0.5 }}
/>
```

### whileHover / whileTap / whileFocus
```tsx
<motion.button
  whileHover={{ scale: 1.05, backgroundColor: "#000" }}
  whileTap={{ scale: 0.95 }}
  whileFocus={{ outline: "2px solid #0070f3" }}
  transition={{ type: "spring", stiffness: 400, damping: 17 }}
/>
```

---

## Entrance Animations (On Mount)

### Fade Up
```tsx
<motion.div
  initial={{ opacity: 0, y: 40 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.7, ease: [0.25, 0.1, 0.25, 1] }}
/>
```

### Fade In (simple)
```tsx
<motion.div
  initial={{ opacity: 0 }}
  animate={{ opacity: 1 }}
  transition={{ duration: 0.5 }}
/>
```

### Slide In From Left
```tsx
<motion.div
  initial={{ opacity: 0, x: -60 }}
  animate={{ opacity: 1, x: 0 }}
  transition={{ duration: 0.7, ease: [0.16, 1, 0.3, 1] }}
/>
```

### Scale In
```tsx
<motion.div
  initial={{ opacity: 0, scale: 0.85 }}
  animate={{ opacity: 1, scale: 1 }}
  transition={{ duration: 0.5, ease: [0.34, 1.56, 0.64, 1] }}
/>
```

### Blur In (modern premium effect)
```tsx
<motion.div
  initial={{ opacity: 0, filter: "blur(10px)" }}
  animate={{ opacity: 1, filter: "blur(0px)" }}
  transition={{ duration: 0.8, ease: [0.25, 0.1, 0.25, 1] }}
/>
```

---

## Scroll-Triggered Animations

### whileInView (simplest approach)
```tsx
<motion.div
  initial={{ opacity: 0, y: 50 }}
  whileInView={{ opacity: 1, y: 0 }}
  viewport={{ once: true, amount: 0.3 }}
  transition={{ duration: 0.7, ease: [0.25, 0.1, 0.25, 1] }}
/>
```

### useInView hook (more control)
```tsx
"use client"
import { useRef } from "react"
import { motion, useInView } from "framer-motion"

export function RevealSection({ children }: { children: React.ReactNode }) {
  const ref = useRef(null)
  const isInView = useInView(ref, { once: true, margin: "-80px 0px" })

  return (
    <motion.div
      ref={ref}
      initial={{ opacity: 0, y: 60 }}
      animate={isInView ? { opacity: 1, y: 0 } : { opacity: 0, y: 60 }}
      transition={{ duration: 0.8, ease: [0.25, 0.1, 0.25, 1] }}
    >
      {children}
    </motion.div>
  )
}
```

---

## Stagger Animations

### Staggered card/list reveal
```tsx
"use client"
import { motion } from "framer-motion"

const containerVariants = {
  hidden: {},
  show: {
    transition: {
      staggerChildren: 0.1,       // 100ms between each child
      delayChildren: 0.2,         // wait 200ms before first child
    }
  }
}

const itemVariants = {
  hidden: { opacity: 0, y: 40 },
  show: {
    opacity: 1,
    y: 0,
    transition: { duration: 0.6, ease: [0.25, 0.1, 0.25, 1] }
  }
}

export function StaggeredGrid({ items }: { items: any[] }) {
  return (
    <motion.div
      className="grid grid-cols-3 gap-6"
      variants={containerVariants}
      initial="hidden"
      whileInView="show"
      viewport={{ once: true, amount: 0.2 }}
    >
      {items.map((item, i) => (
        <motion.div key={i} variants={itemVariants} className="card">
          {item.content}
        </motion.div>
      ))}
    </motion.div>
  )
}
```

### Staggered hero text lines
```tsx
const lineVariants = {
  hidden: { opacity: 0, y: 30, skewY: 3 },
  show: { opacity: 1, y: 0, skewY: 0, transition: { duration: 0.8, ease: [0.16, 1, 0.3, 1] } }
}

export function HeroText() {
  return (
    <motion.div
      variants={{ hidden: {}, show: { transition: { staggerChildren: 0.15 } } }}
      initial="hidden"
      animate="show"
    >
      <div className="overflow-hidden">
        <motion.h1 variants={lineVariants} className="text-7xl font-bold">
          We build
        </motion.h1>
      </div>
      <div className="overflow-hidden">
        <motion.h1 variants={lineVariants} className="text-7xl font-bold">
          premium brands.
        </motion.h1>
      </div>
      <div className="overflow-hidden">
        <motion.p variants={lineVariants} className="text-xl text-gray-400 mt-4">
          Strategy, design, and development that converts.
        </motion.p>
      </div>
    </motion.div>
  )
}
```

---

## Text Animations

### Word-by-word reveal
```tsx
"use client"
import { motion } from "framer-motion"

export function WordReveal({ text }: { text: string }) {
  const words = text.split(" ")

  return (
    <motion.p
      className="text-4xl font-bold"
      initial="hidden"
      whileInView="show"
      viewport={{ once: true }}
    >
      {words.map((word, i) => (
        <span key={i} className="inline-block overflow-hidden mr-2">
          <motion.span
            className="inline-block"
            variants={{
              hidden: { y: "100%", opacity: 0 },
              show: {
                y: 0,
                opacity: 1,
                transition: { duration: 0.6, delay: i * 0.08, ease: [0.16, 1, 0.3, 1] }
              }
            }}
          >
            {word}
          </motion.span>
        </span>
      ))}
    </motion.p>
  )
}
```

### Character-by-character reveal
```tsx
export function CharReveal({ text }: { text: string }) {
  return (
    <motion.h1
      initial="hidden"
      animate="show"
      variants={{ show: { transition: { staggerChildren: 0.03 } } }}
      className="text-6xl font-bold"
    >
      {text.split("").map((char, i) => (
        <motion.span
          key={i}
          className="inline-block"
          variants={{
            hidden: { opacity: 0, y: 20 },
            show: { opacity: 1, y: 0, transition: { duration: 0.4 } }
          }}
        >
          {char === " " ? "\u00A0" : char}
        </motion.span>
      ))}
    </motion.h1>
  )
}
```

### Typewriter effect
```tsx
"use client"
import { motion, useMotionValue, useTransform, animate } from "framer-motion"
import { useEffect, useState } from "react"

export function Typewriter({ phrases }: { phrases: string[] }) {
  const [currentPhrase, setCurrentPhrase] = useState(0)
  const [displayed, setDisplayed] = useState("")
  const [isDeleting, setIsDeleting] = useState(false)

  useEffect(() => {
    const phrase = phrases[currentPhrase]
    const timeout = setTimeout(() => {
      if (!isDeleting) {
        setDisplayed(phrase.substring(0, displayed.length + 1))
        if (displayed.length === phrase.length) {
          setTimeout(() => setIsDeleting(true), 1500)
        }
      } else {
        setDisplayed(phrase.substring(0, displayed.length - 1))
        if (displayed.length === 0) {
          setIsDeleting(false)
          setCurrentPhrase((prev) => (prev + 1) % phrases.length)
        }
      }
    }, isDeleting ? 40 : 70)
    return () => clearTimeout(timeout)
  }, [displayed, isDeleting, currentPhrase, phrases])

  return (
    <span>
      {displayed}
      <motion.span
        animate={{ opacity: [1, 0] }}
        transition={{ duration: 0.5, repeat: Infinity, repeatType: "reverse" }}
        className="inline-block w-0.5 h-[1em] bg-current ml-0.5 align-middle"
      />
    </span>
  )
}
```

---

## Scroll-Linked Animations (useScroll + useTransform)

### Fade out on scroll (hero effect)
```tsx
"use client"
import { useScroll, useTransform, motion } from "framer-motion"
import { useRef } from "react"

export function HeroWithFade() {
  const ref = useRef(null)
  const { scrollYProgress } = useScroll({
    target: ref,
    offset: ["start start", "end start"]
  })

  const opacity = useTransform(scrollYProgress, [0, 0.5], [1, 0])
  const y = useTransform(scrollYProgress, [0, 1], [0, 150])
  const scale = useTransform(scrollYProgress, [0, 0.5], [1, 0.9])

  return (
    <section ref={ref} className="h-screen relative overflow-hidden">
      <motion.div
        style={{ opacity, y, scale }}
        className="absolute inset-0 flex items-center justify-center"
      >
        <h1 className="text-8xl font-bold text-white">Your Headline</h1>
      </motion.div>
    </section>
  )
}
```

### Horizontal scroll-linked progress bar
```tsx
"use client"
import { useScroll, useSpring, motion } from "framer-motion"

export function ScrollProgress() {
  const { scrollYProgress } = useScroll()
  const scaleX = useSpring(scrollYProgress, { stiffness: 100, damping: 30 })

  return (
    <motion.div
      className="fixed top-0 left-0 right-0 h-1 bg-white origin-left z-50"
      style={{ scaleX }}
    />
  )
}
```

---

## AnimatePresence (Mount/Unmount Animations)

### Modal with exit animation
```tsx
"use client"
import { AnimatePresence, motion } from "framer-motion"

export function Modal({ isOpen, onClose, children }: ModalProps) {
  return (
    <AnimatePresence>
      {isOpen && (
        <>
          <motion.div
            className="fixed inset-0 bg-black/60 z-40"
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            exit={{ opacity: 0 }}
            onClick={onClose}
          />
          <motion.div
            className="fixed inset-0 z-50 flex items-center justify-center p-4"
            initial={{ opacity: 0, scale: 0.9, y: 20 }}
            animate={{ opacity: 1, scale: 1, y: 0 }}
            exit={{ opacity: 0, scale: 0.95, y: 10 }}
            transition={{ duration: 0.3, ease: [0.16, 1, 0.3, 1] }}
          >
            <div className="bg-white rounded-2xl p-8 max-w-lg w-full">
              {children}
            </div>
          </motion.div>
        </>
      )}
    </AnimatePresence>
  )
}
```

### Tab content switching
```tsx
<AnimatePresence mode="wait">
  <motion.div
    key={activeTab}
    initial={{ opacity: 0, x: 20 }}
    animate={{ opacity: 1, x: 0 }}
    exit={{ opacity: 0, x: -20 }}
    transition={{ duration: 0.25 }}
  >
    {tabContent[activeTab]}
  </motion.div>
</AnimatePresence>
```

---

## Layout Animations

### Auto-animate layout changes (accordion, expanding cards)
```tsx
// Framer Motion automatically animates between states
<motion.div layout className="card">
  <h3>Title</h3>
  <AnimatePresence>
    {isExpanded && (
      <motion.p
        initial={{ opacity: 0, height: 0 }}
        animate={{ opacity: 1, height: "auto" }}
        exit={{ opacity: 0, height: 0 }}
        transition={{ duration: 0.3 }}
      >
        Expanded content here...
      </motion.p>
    )}
  </AnimatePresence>
</motion.div>
```

### Shared layout (tab indicator that slides)
```tsx
{tabs.map((tab) => (
  <button key={tab} onClick={() => setActive(tab)} className="relative px-4 py-2">
    {tab}
    {active === tab && (
      <motion.div
        layoutId="tab-indicator"
        className="absolute inset-0 bg-black rounded-full -z-10"
        transition={{ type: "spring", stiffness: 500, damping: 35 }}
      />
    )}
  </button>
))}
```

---

## Number Counter Animation

```tsx
"use client"
import { useInView, useMotionValue, useSpring, motion } from "framer-motion"
import { useEffect, useRef } from "react"

export function Counter({ from = 0, to, suffix = "" }: { from?: number; to: number; suffix?: string }) {
  const ref = useRef(null)
  const isInView = useInView(ref, { once: true })
  const motionValue = useMotionValue(from)
  const spring = useSpring(motionValue, { duration: 2000, bounce: 0 })
  const [display, setDisplay] = useState(from)

  useEffect(() => {
    if (isInView) motionValue.set(to)
  }, [isInView, motionValue, to])

  useEffect(() => {
    spring.on("change", (v) => setDisplay(Math.round(v)))
  }, [spring])

  return <span ref={ref}>{display}{suffix}</span>
}

// Usage:
// <Counter to={2400} suffix="+" />  →  animates 0 → 2400+
```

---

## Reduced Motion

```tsx
import { useReducedMotion } from "framer-motion"

function AnimatedCard() {
  const prefersReduced = useReducedMotion()

  return (
    <motion.div
      initial={{ opacity: 0, y: prefersReduced ? 0 : 50 }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true }}
      transition={{ duration: prefersReduced ? 0.1 : 0.7 }}
    />
  )
}
```
