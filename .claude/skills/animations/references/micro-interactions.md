# Micro-Interactions Reference

Hover effects, click feedback, magnetic buttons, cursor effects, and other detail-level animations that make a site feel premium.

---

## Button Animations

### Magnetic button (cursor-following effect)
```tsx
"use client"
import { useRef, useState } from "react"
import { motion } from "framer-motion"

export function MagneticButton({ children, href }: { children: React.ReactNode; href?: string }) {
  const ref = useRef<HTMLAnchorElement>(null)
  const [position, setPosition] = useState({ x: 0, y: 0 })

  const handleMouse = (e: React.MouseEvent) => {
    const { clientX, clientY } = e
    const { height, width, left, top } = ref.current!.getBoundingClientRect()
    const x = clientX - (left + width / 2)
    const y = clientY - (top + height / 2)
    setPosition({ x: x * 0.35, y: y * 0.35 })  // 0.35 = pull strength
  }

  const reset = () => setPosition({ x: 0, y: 0 })

  return (
    <motion.a
      ref={ref}
      href={href}
      onMouseMove={handleMouse}
      onMouseLeave={reset}
      animate={{ x: position.x, y: position.y }}
      transition={{ type: "spring", stiffness: 200, damping: 15, mass: 0.5 }}
      className="inline-flex items-center justify-center px-8 py-4 bg-black text-white rounded-full font-medium cursor-pointer"
    >
      {children}
    </motion.a>
  )
}
```

### Arrow slide-in button
```tsx
<motion.button
  className="group relative overflow-hidden flex items-center gap-2 px-8 py-4 border border-current rounded-full"
  whileHover="hover"
>
  <span>Start Project</span>
  <motion.span
    variants={{
      hover: { x: 4 }
    }}
    transition={{ duration: 0.2 }}
  >
    →
  </motion.span>
  {/* Filling background on hover */}
  <motion.div
    className="absolute inset-0 bg-black -z-10 origin-left"
    variants={{
      hover: { scaleX: 1 }
    }}
    initial={{ scaleX: 0 }}
    transition={{ duration: 0.3, ease: [0.16, 1, 0.3, 1] }}
  />
</motion.button>
```

### Shimmer button (CSS)
```tsx
<button className="relative overflow-hidden px-8 py-4 bg-black text-white rounded-full font-medium group">
  <span className="relative z-10">Get Started</span>
  <span className="absolute inset-0 -translate-x-full group-hover:translate-x-full transition-transform duration-700 bg-gradient-to-r from-transparent via-white/20 to-transparent" />
</button>
```

### Press/tap feedback
```tsx
<motion.button
  whileTap={{ scale: 0.94 }}
  transition={{ type: "spring", stiffness: 600, damping: 20 }}
  className="px-6 py-3 bg-black text-white rounded-lg"
>
  Click me
</motion.button>
```

---

## Card Animations

### Lift on hover
```tsx
<motion.div
  className="rounded-2xl bg-white shadow-md p-6"
  whileHover={{ y: -8, shadow: "0 20px 40px rgba(0,0,0,0.15)" }}
  transition={{ type: "spring", stiffness: 300, damping: 20 }}
>
  {content}
</motion.div>
```

### Image zoom inside card (contains zoom to element)
```tsx
<div className="rounded-2xl overflow-hidden group cursor-pointer">
  <div className="overflow-hidden">
    <img
      src="/project.jpg"
      alt=""
      className="w-full h-64 object-cover transition-transform duration-700 ease-out group-hover:scale-110"
    />
  </div>
  <div className="p-6 transition-colors duration-300 group-hover:bg-gray-50">
    <h3 className="font-bold">Project Name</h3>
    <p className="text-gray-500 mt-1 text-sm">Brand Strategy & Web</p>
  </div>
</div>
```

### Tilt 3D card
```tsx
"use client"
import { useRef } from "react"
import { motion, useMotionValue, useSpring, useTransform } from "framer-motion"

export function TiltCard({ children }: { children: React.ReactNode }) {
  const ref = useRef<HTMLDivElement>(null)
  const x = useMotionValue(0)
  const y = useMotionValue(0)
  const rotateX = useSpring(useTransform(y, [-0.5, 0.5], [12, -12]), { stiffness: 200, damping: 25 })
  const rotateY = useSpring(useTransform(x, [-0.5, 0.5], [-12, 12]), { stiffness: 200, damping: 25 })

  const handleMouse = (e: React.MouseEvent) => {
    const { left, top, width, height } = ref.current!.getBoundingClientRect()
    x.set((e.clientX - left) / width - 0.5)
    y.set((e.clientY - top) / height - 0.5)
  }

  return (
    <motion.div
      ref={ref}
      onMouseMove={handleMouse}
      onMouseLeave={() => { x.set(0); y.set(0) }}
      style={{ rotateX, rotateY, transformStyle: "preserve-3d" }}
      className="rounded-2xl bg-white shadow-xl p-8 cursor-pointer"
    >
      <div style={{ transform: "translateZ(30px)" }}>
        {children}
      </div>
    </motion.div>
  )
}
```

### Reveal overlay on hover (portfolio items)
```tsx
<div className="relative rounded-2xl overflow-hidden group cursor-pointer">
  <img src="/project.jpg" alt="" className="w-full h-80 object-cover" />
  <motion.div
    className="absolute inset-0 bg-black/80 flex flex-col justify-end p-8"
    initial={{ opacity: 0 }}
    whileHover={{ opacity: 1 }}
    transition={{ duration: 0.3 }}
  >
    <motion.h3
      className="text-white text-2xl font-bold"
      initial={{ y: 20 }}
      whileHover={{ y: 0 }}
      transition={{ duration: 0.3, delay: 0.05 }}
    >
      Project Name
    </motion.h3>
    <motion.p
      className="text-white/60 mt-2 text-sm"
      initial={{ y: 20 }}
      whileHover={{ y: 0 }}
      transition={{ duration: 0.3, delay: 0.1 }}
    >
      Brand Identity · Web Design
    </motion.p>
  </motion.div>
</div>
```

---

## Navigation Animations

### Animated underline on nav links
```tsx
<nav>
  {links.map((link) => (
    <a key={link.href} href={link.href} className="relative group px-4 py-2 text-sm font-medium">
      {link.label}
      <span className="absolute bottom-0 left-0 w-0 h-px bg-black transition-all duration-300 group-hover:w-full" />
    </a>
  ))}
</nav>
```

### Animated mobile menu (full-screen)
```tsx
<AnimatePresence>
  {menuOpen && (
    <motion.div
      className="fixed inset-0 bg-black z-50 flex flex-col justify-center px-12"
      initial={{ clipPath: "inset(0 0 100% 0)" }}
      animate={{ clipPath: "inset(0 0 0% 0)" }}
      exit={{ clipPath: "inset(0 0 100% 0)" }}
      transition={{ duration: 0.6, ease: [0.76, 0, 0.24, 1] }}
    >
      <motion.nav
        variants={{ hidden: {}, show: { transition: { staggerChildren: 0.08, delayChildren: 0.3 } } }}
        initial="hidden"
        animate="show"
      >
        {links.map((link) => (
          <div key={link.label} className="overflow-hidden">
            <motion.a
              href={link.href}
              variants={{ hidden: { y: "100%" }, show: { y: 0 } }}
              transition={{ duration: 0.6, ease: [0.16, 1, 0.3, 1] }}
              className="block text-white text-6xl font-bold py-3"
            >
              {link.label}
            </motion.a>
          </div>
        ))}
      </motion.nav>
    </motion.div>
  )}
</AnimatePresence>
```

---

## Custom Cursor

### Dot cursor that follows mouse
```tsx
"use client"
import { useEffect, useRef } from "react"
import { motion, useMotionValue, useSpring } from "framer-motion"

export function CustomCursor() {
  const cursorX = useMotionValue(-100)
  const cursorY = useMotionValue(-100)
  const springConfig = { damping: 25, stiffness: 300 }
  const x = useSpring(cursorX, springConfig)
  const y = useSpring(cursorY, springConfig)

  useEffect(() => {
    const moveCursor = (e: MouseEvent) => {
      cursorX.set(e.clientX - 8)
      cursorY.set(e.clientY - 8)
    }
    window.addEventListener("mousemove", moveCursor)
    return () => window.removeEventListener("mousemove", moveCursor)
  }, [cursorX, cursorY])

  return (
    <>
      {/* Small dot — instant follow */}
      <motion.div
        className="fixed w-4 h-4 bg-white rounded-full pointer-events-none z-[9999] mix-blend-difference"
        style={{ x: cursorX, y: cursorY, translateX: "-50%", translateY: "-50%" }}
      />
      {/* Large ring — spring lag */}
      <motion.div
        className="fixed w-10 h-10 border border-white/40 rounded-full pointer-events-none z-[9999] mix-blend-difference"
        style={{ x, y, translateX: "-50%", translateY: "-50%" }}
      />
    </>
  )
}
```

### Cursor that grows on hoverable elements
```tsx
// Add data-cursor="hover" to any element you want to trigger the grow effect
useEffect(() => {
  const hoverables = document.querySelectorAll("[data-cursor='hover']")
  hoverables.forEach((el) => {
    el.addEventListener("mouseenter", () => setIsHovering(true))
    el.addEventListener("mouseleave", () => setIsHovering(false))
  })
}, [])

// In the cursor component:
<motion.div
  animate={{ scale: isHovering ? 2.5 : 1, opacity: isHovering ? 0.5 : 1 }}
  transition={{ duration: 0.2 }}
  className="fixed w-4 h-4 bg-white rounded-full pointer-events-none z-[9999]"
  style={{ x, y }}
/>
```

---

## Link / Text Hover Effects

### Split-colour hover (text turns two colours)
```tsx
<a href="#" className="relative inline-block overflow-hidden group">
  <span className="block transition-transform duration-300 group-hover:-translate-y-full">
    View Project
  </span>
  <span className="absolute inset-0 translate-y-full transition-transform duration-300 group-hover:translate-y-0 text-gray-400">
    View Project
  </span>
</a>
```

### Strikethrough and re-type hover (creative agencies)
```tsx
<a href="#" className="group relative">
  <span className="group-hover:line-through transition-all duration-200">Services</span>
  <span className="absolute inset-0 opacity-0 group-hover:opacity-100 transition-opacity duration-200 no-underline">
    What We Do
  </span>
</a>
```

---

## Form Input Animations

### Floating label input
```tsx
export function FloatingInput({ label, ...props }: { label: string } & React.InputHTMLAttributes<HTMLInputElement>) {
  const [focused, setFocused] = useState(false)
  const [hasValue, setHasValue] = useState(false)

  return (
    <div className="relative">
      <input
        {...props}
        onFocus={() => setFocused(true)}
        onBlur={(e) => { setFocused(false); setHasValue(e.target.value.length > 0) }}
        className="w-full border-b border-gray-300 pt-6 pb-2 bg-transparent focus:outline-none focus:border-black transition-colors"
      />
      <motion.label
        animate={{
          y: focused || hasValue ? -20 : 0,
          scale: focused || hasValue ? 0.75 : 1,
          color: focused ? "#000" : "#9ca3af"
        }}
        transition={{ duration: 0.2 }}
        className="absolute left-0 top-6 pointer-events-none origin-left"
      >
        {label}
      </motion.label>
      <motion.div
        className="absolute bottom-0 left-0 h-px bg-black origin-left"
        animate={{ scaleX: focused ? 1 : 0 }}
        transition={{ duration: 0.3 }}
      />
    </div>
  )
}
```

---

## Loading States

### Skeleton shimmer (CSS only)
```tsx
// tailwind.config.js — add custom animation:
// animation: { shimmer: "shimmer 1.5s infinite" }
// keyframes: { shimmer: { "0%": { backgroundPosition: "-200% 0" }, "100%": { backgroundPosition: "200% 0" } } }

<div className="w-full h-48 rounded-2xl bg-gradient-to-r from-gray-100 via-gray-200 to-gray-100 bg-[length:200%_100%] animate-shimmer" />
```

### Dots loader
```tsx
<div className="flex gap-1">
  {[0, 1, 2].map((i) => (
    <motion.div
      key={i}
      className="w-2 h-2 bg-current rounded-full"
      animate={{ y: [0, -8, 0] }}
      transition={{ duration: 0.6, delay: i * 0.15, repeat: Infinity }}
    />
  ))}
</div>
```
