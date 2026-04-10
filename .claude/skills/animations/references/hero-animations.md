# Hero Section Animations

Full hero section patterns at agency quality — matching or exceeding wearebrand.io level.

## Pattern 1: Cinematic Hero (Dark, Full-Screen, Parallax BG)

Replicates the wearebrand.io aesthetic: full-screen dark hero, parallax background image, staggered text entrance.

```tsx
"use client"
import { useEffect, useRef } from "react"
import { motion } from "framer-motion"
import { gsap } from "gsap"
import { ScrollTrigger } from "gsap/ScrollTrigger"

gsap.registerPlugin(ScrollTrigger)

const textVariants = {
  hidden: {},
  show: { transition: { staggerChildren: 0.15, delayChildren: 0.4 } }
}

const lineVariant = {
  hidden: { opacity: 0, y: 80, skewY: 4 },
  show: {
    opacity: 1, y: 0, skewY: 0,
    transition: { duration: 1, ease: [0.16, 1, 0.3, 1] }
  }
}

const subtitleVariant = {
  hidden: { opacity: 0, y: 30 },
  show: { opacity: 1, y: 0, transition: { duration: 0.8, ease: [0.25, 0.1, 0.25, 1] } }
}

const ctaVariant = {
  hidden: { opacity: 0, y: 20 },
  show: { opacity: 1, y: 0, transition: { duration: 0.6, delay: 0.2 } }
}

export function CinematicHero() {
  const sectionRef = useRef<HTMLDivElement>(null)
  const bgRef = useRef<HTMLDivElement>(null)
  const imageRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    const ctx = gsap.context(() => {
      // Background parallax — slow drift up
      gsap.to(bgRef.current, {
        yPercent: -20,
        ease: "none",
        scrollTrigger: {
          trigger: sectionRef.current,
          start: "top top",
          end: "bottom top",
          scrub: true,
        }
      })
      // Center image — slight zoom out as page loads, then drift
      gsap.from(imageRef.current, {
        scale: 1.2,
        duration: 2,
        ease: "power3.out",
      })
    })
    return () => ctx.revert()
  }, [])

  return (
    <section
      ref={sectionRef}
      className="relative h-screen w-full overflow-hidden bg-black flex items-end pb-24"
    >
      {/* Parallax background */}
      <div
        ref={bgRef}
        className="absolute inset-0 scale-110"
        style={{ backgroundImage: "url('/hero-bg.jpg')", backgroundSize: "cover", backgroundPosition: "center" }}
      />

      {/* Dark overlay */}
      <div className="absolute inset-0 bg-black/50" />

      {/* Center feature image */}
      <div
        ref={imageRef}
        className="absolute inset-0 flex items-center justify-center"
      >
        <img src="/hero-image.jpg" className="h-[70%] object-contain" alt="" />
      </div>

      {/* Text content */}
      <div className="relative z-10 container mx-auto px-8">
        <motion.div variants={textVariants} initial="hidden" animate="show">
          {/* Main headline — each line clips from below */}
          <div className="overflow-hidden">
            <motion.h1
              variants={lineVariant}
              className="text-[clamp(3rem,8vw,7rem)] font-bold text-white leading-[1.05] tracking-tight"
            >
              Look premium.
            </motion.h1>
          </div>
          <div className="overflow-hidden">
            <motion.h1
              variants={lineVariant}
              className="text-[clamp(3rem,8vw,7rem)] font-bold text-white leading-[1.05] tracking-tight"
            >
              Convert better.
            </motion.h1>
          </div>

          {/* Subtitle */}
          <motion.p
            variants={subtitleVariant}
            className="mt-6 text-white/60 text-lg max-w-md"
          >
            Strategy, design, and development that makes your brand unforgettable.
          </motion.p>

          {/* CTA */}
          <motion.div variants={ctaVariant} className="mt-10 flex gap-4">
            <motion.a
              href="#"
              className="px-8 py-4 bg-white text-black font-semibold rounded-full text-sm tracking-wide"
              whileHover={{ scale: 1.04, backgroundColor: "#f0f0f0" }}
              whileTap={{ scale: 0.97 }}
              transition={{ type: "spring", stiffness: 400, damping: 17 }}
            >
              START YOUR PROJECT
            </motion.a>
            <motion.a
              href="#work"
              className="px-8 py-4 border border-white/30 text-white font-semibold rounded-full text-sm tracking-wide"
              whileHover={{ borderColor: "rgba(255,255,255,0.8)", scale: 1.02 }}
              transition={{ duration: 0.2 }}
            >
              SEE OUR WORK
            </motion.a>
          </motion.div>
        </motion.div>
      </div>

      {/* Scroll indicator */}
      <motion.div
        className="absolute bottom-8 right-8 flex flex-col items-center gap-2 text-white/40 text-xs tracking-widest"
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
        transition={{ delay: 1.5, duration: 0.8 }}
      >
        <motion.div
          className="w-px h-16 bg-white/20 origin-top"
          animate={{ scaleY: [0, 1, 0] }}
          transition={{ duration: 1.5, repeat: Infinity, ease: "linear" }}
        />
        SCROLL
      </motion.div>
    </section>
  )
}
```

---

## Pattern 2: Split-Screen Hero

Left text, right full-bleed image with parallax.

```tsx
"use client"
import { motion } from "framer-motion"
import { useEffect, useRef } from "react"
import { gsap } from "gsap"
import { ScrollTrigger } from "gsap/ScrollTrigger"

gsap.registerPlugin(ScrollTrigger)

export function SplitHero() {
  const imgRef = useRef<HTMLImageElement>(null)

  useEffect(() => {
    const ctx = gsap.context(() => {
      gsap.from(imgRef.current, {
        scale: 1.3,
        duration: 1.8,
        ease: "power3.out",
      })
    })
    return () => ctx.revert()
  }, [])

  return (
    <section className="h-screen grid grid-cols-2 overflow-hidden">
      {/* Left: Text */}
      <div className="flex flex-col justify-center px-16 bg-black">
        <motion.span
          className="text-white/40 text-sm tracking-[0.3em] uppercase mb-6"
          initial={{ opacity: 0, x: -20 }}
          animate={{ opacity: 1, x: 0 }}
          transition={{ duration: 0.6, delay: 0.2 }}
        >
          Digital Agency
        </motion.span>
        <div className="overflow-hidden">
          <motion.h1
            className="text-7xl font-bold text-white leading-tight"
            initial={{ y: "100%" }}
            animate={{ y: 0 }}
            transition={{ duration: 1, delay: 0.4, ease: [0.16, 1, 0.3, 1] }}
          >
            We craft<br />digital<br />experiences.
          </motion.h1>
        </div>
        <motion.div
          className="mt-10"
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.7, delay: 0.9 }}
        >
          <a href="#" className="group flex items-center gap-3 text-white font-medium">
            <span>View Our Work</span>
            <motion.span
              className="inline-block"
              animate={{ x: [0, 5, 0] }}
              transition={{ duration: 1.5, repeat: Infinity }}
            >
              →
            </motion.span>
          </a>
        </motion.div>
      </div>

      {/* Right: Image */}
      <div className="overflow-hidden relative">
        <img
          ref={imgRef}
          src="/split-hero.jpg"
          alt=""
          className="w-full h-full object-cover"
        />
        <div className="absolute inset-0 bg-black/20" />
      </div>
    </section>
  )
}
```

---

## Pattern 3: Text-Only Bold Hero (Typographic Impact)

High impact with massive typography and subtle background animation.

```tsx
"use client"
import { motion } from "framer-motion"

const words = ["Strategy.", "Design.", "Development."]

export function TypographicHero() {
  return (
    <section className="min-h-screen bg-black flex flex-col justify-center px-8 md:px-20 relative overflow-hidden">
      {/* Animated gradient orb background */}
      <motion.div
        className="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[800px] h-[800px] rounded-full opacity-10"
        style={{
          background: "radial-gradient(circle, #ffffff 0%, transparent 70%)"
        }}
        animate={{
          scale: [1, 1.2, 1],
          opacity: [0.05, 0.12, 0.05]
        }}
        transition={{ duration: 6, repeat: Infinity, ease: "easeInOut" }}
      />

      {/* Overline */}
      <motion.p
        className="text-white/40 text-sm tracking-[0.4em] uppercase mb-12"
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
        transition={{ duration: 0.8, delay: 0.2 }}
      >
        Premium Digital Agency
      </motion.p>

      {/* Stacked bold words */}
      <div>
        {words.map((word, i) => (
          <div key={word} className="overflow-hidden">
            <motion.h1
              className="text-[clamp(4rem,10vw,9rem)] font-black text-white leading-[0.95] tracking-tight"
              initial={{ y: "110%", opacity: 0 }}
              animate={{ y: 0, opacity: 1 }}
              transition={{
                duration: 0.9,
                delay: 0.3 + i * 0.12,
                ease: [0.16, 1, 0.3, 1]
              }}
            >
              {word}
            </motion.h1>
          </div>
        ))}
      </div>

      {/* Bottom row */}
      <motion.div
        className="mt-16 flex items-end justify-between"
        initial={{ opacity: 0, y: 30 }}
        animate={{ opacity: 1, y: 0 }}
        transition={{ duration: 0.8, delay: 0.9 }}
      >
        <p className="text-white/50 max-w-xs text-sm leading-relaxed">
          We build brands that stand out — turning beautiful design into high-converting digital machines.
        </p>
        <motion.a
          href="#"
          className="text-white border-b border-white/30 pb-1 text-sm tracking-widest uppercase"
          whileHover={{ borderColor: "rgba(255,255,255,1)" }}
          transition={{ duration: 0.2 }}
        >
          Start a project
        </motion.a>
      </motion.div>
    </section>
  )
}
```

---

## Pattern 4: Video/Reel Background Hero

For agencies with showreels or cinematic video.

```tsx
"use client"
import { useEffect, useRef, useState } from "react"
import { motion } from "framer-motion"

export function VideoHero() {
  const [loaded, setLoaded] = useState(false)

  return (
    <section className="relative h-screen overflow-hidden bg-black">
      {/* Video background */}
      <video
        autoPlay
        muted
        loop
        playsInline
        onCanPlay={() => setLoaded(true)}
        className="absolute inset-0 w-full h-full object-cover opacity-50"
      >
        <source src="/showreel.mp4" type="video/mp4" />
      </video>

      {/* Gradient overlay */}
      <div className="absolute inset-0 bg-gradient-to-t from-black via-black/30 to-transparent" />

      {/* Content */}
      <motion.div
        className="relative z-10 h-full flex flex-col justify-end pb-24 px-12"
        initial={{ opacity: 0 }}
        animate={{ opacity: loaded ? 1 : 0 }}
        transition={{ duration: 1.2 }}
      >
        <motion.h1
          className="text-8xl font-bold text-white max-w-3xl leading-tight"
          initial={{ opacity: 0, y: 60 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 1, delay: 0.3, ease: [0.16, 1, 0.3, 1] }}
        >
          We are a creative studio.
        </motion.h1>
        <motion.p
          className="mt-4 text-white/60 text-xl"
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.8, delay: 0.7 }}
        >
          Building brands that leave a mark.
        </motion.p>
      </motion.div>

      {/* Animated scroll cue */}
      <motion.div
        className="absolute bottom-8 left-1/2 -translate-x-1/2"
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
        transition={{ delay: 1.5 }}
      >
        <motion.div
          className="w-6 h-10 border-2 border-white/30 rounded-full flex justify-center pt-2"
        >
          <motion.div
            className="w-1 h-2 bg-white rounded-full"
            animate={{ y: [0, 12, 0], opacity: [1, 0, 1] }}
            transition={{ duration: 1.5, repeat: Infinity }}
          />
        </motion.div>
      </motion.div>
    </section>
  )
}
```

---

## Scroll Indicator Variants

### Animated line
```tsx
<motion.div className="flex flex-col items-center gap-2 text-white/40 text-xs tracking-widest">
  <motion.div
    className="w-px bg-white/30 origin-top"
    initial={{ height: 0 }}
    animate={{ height: 60 }}
    transition={{ duration: 0.8, delay: 2, ease: "easeOut" }}
  />
  <motion.span initial={{ opacity: 0 }} animate={{ opacity: 1 }} transition={{ delay: 2.5 }}>
    SCROLL
  </motion.span>
</motion.div>
```

### Bouncing chevron
```tsx
<motion.div
  animate={{ y: [0, 8, 0] }}
  transition={{ duration: 1.2, repeat: Infinity, ease: "easeInOut" }}
  className="text-white/40"
>
  ↓
</motion.div>
```

---

## Loading Screen / Intro Sequence

```tsx
"use client"
import { AnimatePresence, motion } from "framer-motion"
import { useState, useEffect } from "react"

export function IntroLoader({ children }: { children: React.ReactNode }) {
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    const timer = setTimeout(() => setLoading(false), 2000)
    return () => clearTimeout(timer)
  }, [])

  return (
    <>
      <AnimatePresence>
        {loading && (
          <motion.div
            className="fixed inset-0 z-[100] bg-black flex items-center justify-center"
            exit={{ y: "-100%" }}
            transition={{ duration: 0.8, ease: [0.76, 0, 0.24, 1], delay: 0.2 }}
          >
            <motion.div
              className="text-white text-2xl font-bold tracking-widest"
              initial={{ opacity: 0, y: 20 }}
              animate={{ opacity: 1, y: 0 }}
              exit={{ opacity: 0 }}
              transition={{ duration: 0.5 }}
            >
              BRAND.
            </motion.div>
          </motion.div>
        )}
      </AnimatePresence>
      <motion.div
        initial={{ opacity: 0 }}
        animate={{ opacity: loading ? 0 : 1 }}
        transition={{ duration: 0.5, delay: 0.1 }}
      >
        {children}
      </motion.div>
    </>
  )
}
```
