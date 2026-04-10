# Scroll Animations Reference

Scroll-triggered reveals, counters, progress indicators, pinned sections, and scroll-linked transforms.

---

## Setup: Lenis Smooth Scroll

Add this once at the app level for buttery scroll. Works standalone or with GSAP.

```tsx
// components/smooth-scroll-provider.tsx
"use client"
import { useEffect } from "react"
import Lenis from "lenis"
import { gsap } from "gsap"
import { ScrollTrigger } from "gsap/ScrollTrigger"

gsap.registerPlugin(ScrollTrigger)

export function SmoothScrollProvider({ children }: { children: React.ReactNode }) {
  useEffect(() => {
    const lenis = new Lenis({
      duration: 1.2,
      easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
      smoothWheel: true,
    })

    // Sync with GSAP if using ScrollTrigger
    lenis.on("scroll", ScrollTrigger.update)
    gsap.ticker.add((time) => lenis.raf(time * 1000))
    gsap.ticker.lagSmoothing(0)

    return () => {
      lenis.destroy()
    }
  }, [])

  return <>{children}</>
}
```

```tsx
// app/layout.tsx
import { SmoothScrollProvider } from "@/components/smooth-scroll-provider"

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <SmoothScrollProvider>{children}</SmoothScrollProvider>
      </body>
    </html>
  )
}
```

---

## Scroll Reveal Patterns

### Universal reveal wrapper component
```tsx
// components/reveal.tsx
"use client"
import { motion } from "framer-motion"

type RevealProps = {
  children: React.ReactNode
  delay?: number
  direction?: "up" | "down" | "left" | "right" | "fade"
  className?: string
}

const directions = {
  up:    { opacity: 0, y: 50 },
  down:  { opacity: 0, y: -50 },
  left:  { opacity: 0, x: 60 },
  right: { opacity: 0, x: -60 },
  fade:  { opacity: 0 },
}

export function Reveal({ children, delay = 0, direction = "up", className }: RevealProps) {
  return (
    <motion.div
      className={className}
      initial={directions[direction]}
      whileInView={{ opacity: 1, x: 0, y: 0 }}
      viewport={{ once: true, amount: 0.25 }}
      transition={{
        duration: 0.75,
        delay,
        ease: [0.25, 0.1, 0.25, 1]
      }}
    >
      {children}
    </motion.div>
  )
}

// Usage:
// <Reveal delay={0.1} direction="up"><h2>Section Title</h2></Reveal>
// <Reveal delay={0.2} direction="left"><p>Body text</p></Reveal>
```

### Clip-path wipe reveal
```tsx
"use client"
import { motion } from "framer-motion"

export function WipeReveal({ children }: { children: React.ReactNode }) {
  return (
    <motion.div
      initial={{ clipPath: "inset(0 100% 0 0)" }}
      whileInView={{ clipPath: "inset(0 0% 0 0)" }}
      viewport={{ once: true, amount: 0.5 }}
      transition={{ duration: 1, ease: [0.76, 0, 0.24, 1] }}
    >
      {children}
    </motion.div>
  )
}
```

---

## Scroll Progress Indicators

### Page progress bar (top of screen)
```tsx
"use client"
import { useScroll, useSpring, motion } from "framer-motion"

export function ScrollProgressBar() {
  const { scrollYProgress } = useScroll()
  const scaleX = useSpring(scrollYProgress, { stiffness: 100, damping: 30, restDelta: 0.001 })

  return (
    <motion.div
      className="fixed top-0 left-0 right-0 h-0.5 bg-white z-50 origin-left"
      style={{ scaleX }}
    />
  )
}
```

### Vertical sidebar progress (like wearebrand.io)
```tsx
"use client"
import { useScroll, useTransform, motion } from "framer-motion"

export function VerticalProgress() {
  const { scrollYProgress } = useScroll()
  const height = useTransform(scrollYProgress, [0, 1], ["0%", "100%"])

  return (
    <div className="fixed right-8 top-1/2 -translate-y-1/2 h-32 w-px bg-white/20 z-50">
      <motion.div className="w-full bg-white origin-top" style={{ height }} />
    </div>
  )
}
```

### Section-based dot navigation
```tsx
"use client"
import { useEffect, useState } from "react"
import { motion } from "framer-motion"

const sections = ["hero", "work", "about", "contact"]

export function DotNav() {
  const [active, setActive] = useState(0)

  useEffect(() => {
    const observers = sections.map((id, i) => {
      const el = document.getElementById(id)
      if (!el) return null
      const obs = new IntersectionObserver(
        ([entry]) => { if (entry.isIntersecting) setActive(i) },
        { threshold: 0.5 }
      )
      obs.observe(el)
      return obs
    })
    return () => observers.forEach((obs) => obs?.disconnect())
  }, [])

  return (
    <div className="fixed right-8 top-1/2 -translate-y-1/2 flex flex-col gap-3 z-50">
      {sections.map((_, i) => (
        <button
          key={i}
          onClick={() => document.getElementById(sections[i])?.scrollIntoView({ behavior: "smooth" })}
          className="w-1.5 h-1.5 rounded-full bg-white/30 relative"
        >
          {active === i && (
            <motion.div
              layoutId="dot-active"
              className="absolute inset-0 rounded-full bg-white"
              transition={{ type: "spring", stiffness: 500, damping: 30 }}
            />
          )}
        </button>
      ))}
    </div>
  )
}
```

---

## Animated Stats Section

```tsx
"use client"
import { useInView, useMotionValue, useSpring, motion } from "framer-motion"
import { useEffect, useRef, useState } from "react"

function AnimatedNumber({ value, suffix = "" }: { value: number; suffix?: string }) {
  const ref = useRef(null)
  const isInView = useInView(ref, { once: true })
  const mv = useMotionValue(0)
  const spring = useSpring(mv, { duration: 2000, bounce: 0 })
  const [display, setDisplay] = useState(0)

  useEffect(() => {
    if (isInView) mv.set(value)
  }, [isInView, mv, value])

  useEffect(() => spring.on("change", (v) => setDisplay(Math.round(v))), [spring])

  return <span ref={ref}>{display.toLocaleString()}{suffix}</span>
}

const stats = [
  { value: 240, suffix: "+", label: "Projects Delivered" },
  { value: 98, suffix: "%", label: "Client Satisfaction" },
  { value: 12, suffix: "", label: "Years of Experience" },
  { value: 40, suffix: "+", label: "Awards Won" },
]

export function StatsSection() {
  return (
    <section className="py-32 bg-black">
      <div className="container mx-auto px-8">
        <div className="grid grid-cols-2 md:grid-cols-4 gap-12">
          {stats.map((stat, i) => (
            <motion.div
              key={stat.label}
              className="text-center"
              initial={{ opacity: 0, y: 40 }}
              whileInView={{ opacity: 1, y: 0 }}
              viewport={{ once: true }}
              transition={{ duration: 0.7, delay: i * 0.1 }}
            >
              <div className="text-6xl font-bold text-white">
                <AnimatedNumber value={stat.value} suffix={stat.suffix} />
              </div>
              <p className="text-white/40 mt-3 text-sm tracking-wide uppercase">{stat.label}</p>
            </motion.div>
          ))}
        </div>
      </div>
    </section>
  )
}
```

---

## Scroll-Linked Transforms

### Navbar shrink on scroll
```tsx
"use client"
import { useScroll, useTransform, motion } from "framer-motion"

export function AnimatedNav() {
  const { scrollY } = useScroll()
  const paddingY = useTransform(scrollY, [0, 80], [24, 12])
  const background = useTransform(scrollY, [0, 80], ["rgba(0,0,0,0)", "rgba(0,0,0,0.95)"])
  const borderOpacity = useTransform(scrollY, [0, 80], [0, 0.1])

  return (
    <motion.nav
      className="fixed top-0 left-0 right-0 z-40 flex items-center justify-between px-8"
      style={{ paddingTop: paddingY, paddingBottom: paddingY, backgroundColor: background, borderBottom: `1px solid rgba(255,255,255,${borderOpacity})` }}
    >
      <span className="text-white font-bold">BRAND.</span>
      {/* nav links */}
    </motion.nav>
  )
}
```

### Section colour shift on scroll (dark → light)
```tsx
"use client"
import { useScroll, useTransform, motion } from "framer-motion"
import { useRef } from "react"

export function ColorShiftSection() {
  const ref = useRef(null)
  const { scrollYProgress } = useScroll({ target: ref, offset: ["start end", "end start"] })
  const bg = useTransform(scrollYProgress, [0.2, 0.6], ["#000000", "#ffffff"])
  const color = useTransform(scrollYProgress, [0.2, 0.6], ["#ffffff", "#000000"])

  return (
    <motion.section ref={ref} style={{ backgroundColor: bg, color }} className="py-32 px-8">
      <h2 className="text-5xl font-bold">This section changes colour</h2>
    </motion.section>
  )
}
```

---

## Infinite Ticker / Marquee (Clients / Services strip)

```tsx
"use client"
import { motion } from "framer-motion"

const items = ["Brand Identity", "Web Design", "Development", "SEO", "Content", "Strategy"]

export function Ticker() {
  return (
    <div className="overflow-hidden border-y border-white/10 py-5">
      <motion.div
        className="flex gap-16 whitespace-nowrap"
        animate={{ x: ["0%", "-50%"] }}
        transition={{ duration: 20, repeat: Infinity, ease: "linear" }}
      >
        {/* Duplicate for seamless loop */}
        {[...items, ...items].map((item, i) => (
          <span key={i} className="text-white/60 text-sm tracking-widest uppercase flex items-center gap-4">
            {item}
            <span className="w-1.5 h-1.5 rounded-full bg-white/30 inline-block" />
          </span>
        ))}
      </motion.div>
    </div>
  )
}
```

---

## Scroll-Triggered Image Gallery (Masonry fade-in)

```tsx
"use client"
import { motion } from "framer-motion"

const projects = [
  { src: "/p1.jpg", title: "Brand Identity" },
  { src: "/p2.jpg", title: "E-commerce" },
  { src: "/p3.jpg", title: "SaaS Dashboard" },
  { src: "/p4.jpg", title: "Mobile App" },
  { src: "/p5.jpg", title: "Landing Page" },
  { src: "/p6.jpg", title: "Marketing Site" },
]

export function ProjectGrid() {
  return (
    <section className="py-24 px-8">
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        {projects.map((project, i) => (
          <motion.div
            key={project.title}
            className="group relative overflow-hidden rounded-2xl aspect-[4/3] cursor-pointer"
            initial={{ opacity: 0, y: 60, scale: 0.96 }}
            whileInView={{ opacity: 1, y: 0, scale: 1 }}
            viewport={{ once: true, amount: 0.2 }}
            transition={{ duration: 0.8, delay: (i % 3) * 0.1, ease: [0.25, 0.1, 0.25, 1] }}
            whileHover={{ scale: 1.02 }}
          >
            <img
              src={project.src}
              alt={project.title}
              className="w-full h-full object-cover transition-transform duration-700 group-hover:scale-110"
            />
            <motion.div
              className="absolute inset-0 bg-black/70 flex items-end p-6"
              initial={{ opacity: 0 }}
              whileHover={{ opacity: 1 }}
              transition={{ duration: 0.3 }}
            >
              <h3 className="text-white text-xl font-bold">{project.title}</h3>
            </motion.div>
          </motion.div>
        ))}
      </div>
    </section>
  )
}
```

---

## Testimonial Carousel (Auto-advancing with animation)

```tsx
"use client"
import { AnimatePresence, motion } from "framer-motion"
import { useEffect, useState } from "react"

const testimonials = [
  { quote: "They transformed our brand completely.", name: "Sarah K.", company: "Acme Corp" },
  { quote: "Best agency we've ever worked with.", name: "James R.", company: "StartupXYZ" },
  { quote: "Delivered beyond our expectations.", name: "Maya L.", company: "BlueWave" },
]

export function TestimonialCarousel() {
  const [current, setCurrent] = useState(0)

  useEffect(() => {
    const interval = setInterval(() => setCurrent((c) => (c + 1) % testimonials.length), 4000)
    return () => clearInterval(interval)
  }, [])

  return (
    <div className="relative overflow-hidden py-20">
      <AnimatePresence mode="wait">
        <motion.blockquote
          key={current}
          initial={{ opacity: 0, y: 30 }}
          animate={{ opacity: 1, y: 0 }}
          exit={{ opacity: 0, y: -30 }}
          transition={{ duration: 0.5, ease: [0.25, 0.1, 0.25, 1] }}
          className="text-center max-w-2xl mx-auto"
        >
          <p className="text-3xl font-light text-white leading-relaxed">
            "{testimonials[current].quote}"
          </p>
          <footer className="mt-8 text-white/40">
            {testimonials[current].name} — {testimonials[current].company}
          </footer>
        </motion.blockquote>
      </AnimatePresence>

      {/* Dots */}
      <div className="flex justify-center gap-2 mt-10">
        {testimonials.map((_, i) => (
          <button
            key={i}
            onClick={() => setCurrent(i)}
            className={`w-1.5 h-1.5 rounded-full transition-colors duration-300 ${i === current ? "bg-white" : "bg-white/20"}`}
          />
        ))}
      </div>
    </div>
  )
}
```
