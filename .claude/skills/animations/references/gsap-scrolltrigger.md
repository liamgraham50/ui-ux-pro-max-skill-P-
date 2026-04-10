# GSAP + ScrollTrigger Reference

Advanced scroll-driven animations, parallax, and timeline sequences using GSAP.

## Installation & Setup

```bash
npm install gsap
```

```tsx
// Import in component
import { gsap } from "gsap"
import { ScrollTrigger } from "gsap/ScrollTrigger"

// Register ONCE at app level (e.g. app/layout.tsx or providers.tsx)
if (typeof window !== "undefined") {
  gsap.registerPlugin(ScrollTrigger)
}
```

**Next.js caveat:** Always use GSAP inside `useEffect` — never at module level, as it requires the browser DOM.

---

## ScrollTrigger Basics

### Anatomy of a ScrollTrigger
```tsx
useEffect(() => {
  const ctx = gsap.context(() => {
    gsap.to(".target", {
      // what to animate
      y: -100,
      opacity: 0,

      scrollTrigger: {
        trigger: ".section",     // element that triggers the animation
        start: "top 80%",        // "element edge" "viewport edge"
        end: "bottom 20%",       // when animation ends
        scrub: false,            // true = tied to scroll position, false = plays once
        pin: false,              // pin the trigger element during animation
        markers: false,          // show debug markers (set true during dev)
        toggleActions: "play none none reverse",
        // play = forward, pause, resume, reverse, reset, restart, complete, none
        // order: onEnter onLeave onEnterBack onLeaveBack
      }
    })
  })
  return () => ctx.revert() // cleanup on unmount
}, [])
```

### start / end syntax
```
"top bottom"   → trigger top hits viewport bottom (element enters view)
"top center"   → trigger top hits viewport center
"top top"      → trigger top hits viewport top
"center center"→ trigger center hits viewport center
"80% 80%"      → 80% down trigger hits 80% down viewport
"+=300"        → 300px past the start point
```

---

## Parallax Effects

### Background parallax (image moves slower than scroll)
```tsx
"use client"
import { useEffect, useRef } from "react"
import { gsap } from "gsap"
import { ScrollTrigger } from "gsap/ScrollTrigger"

gsap.registerPlugin(ScrollTrigger)

export function ParallaxSection() {
  const sectionRef = useRef<HTMLDivElement>(null)
  const bgRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    const ctx = gsap.context(() => {
      gsap.to(bgRef.current, {
        yPercent: -25,           // moves up 25% of its height
        ease: "none",
        scrollTrigger: {
          trigger: sectionRef.current,
          start: "top bottom",
          end: "bottom top",
          scrub: true,           // tied to scroll — smooth parallax
        }
      })
    })
    return () => ctx.revert()
  }, [])

  return (
    <section ref={sectionRef} className="relative h-screen overflow-hidden">
      <div
        ref={bgRef}
        className="absolute inset-0 scale-125 bg-cover bg-center"
        style={{ backgroundImage: "url('/hero-bg.jpg')" }}
      />
      <div className="relative z-10 flex items-center justify-center h-full">
        <h1 className="text-white text-7xl font-bold">Content Here</h1>
      </div>
    </section>
  )
}
```

### Multi-layer parallax (depth effect like wearebrand.io)
```tsx
useEffect(() => {
  const ctx = gsap.context(() => {
    // Background moves slowest
    gsap.to(".layer-bg", {
      yPercent: -15,
      ease: "none",
      scrollTrigger: { trigger: ".hero", start: "top top", end: "bottom top", scrub: true }
    })
    // Midground moves medium
    gsap.to(".layer-mid", {
      yPercent: -30,
      ease: "none",
      scrollTrigger: { trigger: ".hero", start: "top top", end: "bottom top", scrub: true }
    })
    // Foreground text moves fastest
    gsap.to(".layer-text", {
      yPercent: -60,
      opacity: 0,
      ease: "none",
      scrollTrigger: { trigger: ".hero", start: "top top", end: "center top", scrub: true }
    })
  })
  return () => ctx.revert()
}, [])
```

### Image zoom on scroll (like agency hero backgrounds)
```tsx
useEffect(() => {
  const ctx = gsap.context(() => {
    gsap.fromTo(".hero-image",
      { scale: 1.15 },
      {
        scale: 1,
        ease: "none",
        scrollTrigger: {
          trigger: ".hero",
          start: "top top",
          end: "bottom top",
          scrub: true,
        }
      }
    )
  })
  return () => ctx.revert()
}, [])
```

---

## Scroll-Triggered Reveals

### Fade up on enter
```tsx
useEffect(() => {
  const ctx = gsap.context(() => {
    gsap.utils.toArray(".reveal").forEach((el: any) => {
      gsap.from(el, {
        opacity: 0,
        y: 60,
        duration: 0.9,
        ease: "power3.out",
        scrollTrigger: {
          trigger: el,
          start: "top 85%",
          toggleActions: "play none none none",
        }
      })
    })
  })
  return () => ctx.revert()
}, [])
```

### Staggered grid reveal
```tsx
useEffect(() => {
  const ctx = gsap.context(() => {
    gsap.from(".grid-item", {
      opacity: 0,
      y: 50,
      duration: 0.7,
      stagger: 0.1,           // 100ms between each item
      ease: "power3.out",
      scrollTrigger: {
        trigger: ".grid",
        start: "top 80%",
      }
    })
  })
  return () => ctx.revert()
}, [])
```

---

## Pinned Sections (Scroll Storytelling)

### Pin while content animates (fullscreen takeover)
```tsx
useEffect(() => {
  const ctx = gsap.context(() => {
    const tl = gsap.timeline({
      scrollTrigger: {
        trigger: ".pin-section",
        start: "top top",
        end: "+=300%",           // pin for 3 screen heights of scroll
        pin: true,               // pin the element
        scrub: 1,                // smooth scrub (1 = 1s lag)
        anticipatePin: 1,
      }
    })

    tl
      .from(".step-1", { opacity: 0, y: 100 })
      .to(".step-1", { opacity: 0, y: -100 }, "+=0.5")
      .from(".step-2", { opacity: 0, y: 100 })
      .to(".step-2", { opacity: 0, y: -100 }, "+=0.5")
      .from(".step-3", { opacity: 0, y: 100 })
  })
  return () => ctx.revert()
}, [])
```

### Horizontal scroll section
```tsx
useEffect(() => {
  const ctx = gsap.context(() => {
    const panels = gsap.utils.toArray(".panel")

    gsap.to(panels, {
      xPercent: -100 * (panels.length - 1),
      ease: "none",
      scrollTrigger: {
        trigger: ".horizontal-container",
        pin: true,
        scrub: 1,
        snap: 1 / (panels.length - 1),
        end: () => "+=" + document.querySelector(".horizontal-container")!.scrollWidth,
      }
    })
  })
  return () => ctx.revert()
}, [])
```

---

## Text Animations

### Line-by-line reveal
```tsx
import { SplitText } from "gsap/SplitText"  // GSAP Club (paid) or use manual splitting

// Manual line splitting approach (free):
useEffect(() => {
  const ctx = gsap.context(() => {
    const lines = gsap.utils.toArray(".headline .line")
    gsap.from(lines, {
      y: "100%",
      opacity: 0,
      duration: 0.8,
      stagger: 0.12,
      ease: "power4.out",
      scrollTrigger: {
        trigger: ".headline",
        start: "top 80%",
      }
    })
  })
  return () => ctx.revert()
}, [])
```

```tsx
// JSX — wrap each line in overflow-hidden for clean clip effect
<h1 className="headline">
  <div className="overflow-hidden"><span className="line block">We build</span></div>
  <div className="overflow-hidden"><span className="line block">premium brands.</span></div>
</h1>
```

### Scramble text effect (futuristic)
```tsx
// No extra plugin needed — manual implementation
export function ScrambleText({ text }: { text: string }) {
  const ref = useRef<HTMLSpanElement>(null)
  const chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789"

  useEffect(() => {
    const el = ref.current!
    let frame = 0
    const totalFrames = 20
    const interval = setInterval(() => {
      el.textContent = text
        .split("")
        .map((char, i) => {
          if (i < (frame / totalFrames) * text.length) return char
          return chars[Math.floor(Math.random() * chars.length)]
        })
        .join("")
      frame++
      if (frame > totalFrames) clearInterval(interval)
    }, 40)
    return () => clearInterval(interval)
  }, [text])

  return <span ref={ref}>{text}</span>
}
```

---

## Number / Counter Animation

```tsx
useEffect(() => {
  const ctx = gsap.context(() => {
    gsap.utils.toArray(".stat-number").forEach((el: any) => {
      const target = parseFloat(el.getAttribute("data-target"))
      gsap.from({ value: 0 }, {
        value: target,
        duration: 2,
        ease: "power2.out",
        onUpdate: function() {
          el.textContent = Math.round(this.targets()[0].value).toLocaleString()
        },
        scrollTrigger: {
          trigger: el,
          start: "top 80%",
          toggleActions: "play none none none",
        }
      })
    })
  })
  return () => ctx.revert()
}, [])
```

```tsx
// JSX
<span className="stat-number" data-target="2400">0</span>
```

---

## Image Reveal (Clip Path Wipe)

```tsx
useEffect(() => {
  const ctx = gsap.context(() => {
    gsap.from(".reveal-image", {
      clipPath: "inset(0 100% 0 0)",   // wipe from left
      duration: 1.2,
      ease: "power4.inOut",
      scrollTrigger: {
        trigger: ".reveal-image",
        start: "top 75%",
      }
    })
  })
  return () => ctx.revert()
}, [])
```

---

## Lenis + GSAP Integration

To use GSAP ScrollTrigger with Lenis smooth scroll, sync them:

```tsx
"use client"
import { useEffect } from "react"
import Lenis from "lenis"
import { gsap } from "gsap"
import { ScrollTrigger } from "gsap/ScrollTrigger"

gsap.registerPlugin(ScrollTrigger)

export function SmoothScrollProvider({ children }: { children: React.ReactNode }) {
  useEffect(() => {
    const lenis = new Lenis()

    // Sync Lenis with GSAP ScrollTrigger
    lenis.on("scroll", ScrollTrigger.update)

    gsap.ticker.add((time) => {
      lenis.raf(time * 1000)
    })

    gsap.ticker.lagSmoothing(0)

    return () => {
      lenis.destroy()
      gsap.ticker.remove(lenis.raf)
    }
  }, [])

  return <>{children}</>
}
```

---

## Performance Tips

1. **Always use `gsap.context()`** — it scopes selectors and auto-cleans on unmount
2. **Return `ctx.revert()`** in the cleanup — prevents memory leaks
3. **Use `will-change: transform`** CSS on parallax elements — tells browser to GPU-composite
4. **`scrub: true` vs `scrub: 1`** — `true` = instant snap, number = seconds of lag (smoother)
5. **Avoid animating `top/left/margin`** — only animate `transform: translate` (use `x`, `y` in GSAP)
6. **`invalidateOnRefresh: true`** on ScrollTriggers that depend on element dimensions

```tsx
scrollTrigger: {
  trigger: el,
  start: "top 80%",
  invalidateOnRefresh: true,  // recalculate on window resize
}
```
