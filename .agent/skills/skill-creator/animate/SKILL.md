---
name: semantic-animation
description: Add purposeful, justified animations to web designs. Use when you have an existing design (HTML, React, or design system) and want to layer in motion that serves a clear purpose — guiding attention, providing feedback, revealing information, or reinforcing brand tempo. Skip decorative animations; every motion must answer "why is this moving?" Trigger whenever a user provides a design and mentions animations, motion, or wants to make something feel more dynamic, OR when they say animations feel unaesthetic and want a more thoughtful approach.
---

# Semantic Animation Skill

Animation should never be decorative. Every motion on a page should serve one of these semantic purposes:

1. **Attention Guidance** — Lead the eye to critical information or interaction points
2. **Feedback** — Confirm user actions (hover, click, form validation, success states)
3. **Information Reveal** — Animate in data, sections, or details as the user needs them
4. **Tempo / Brand Rhythm** — Set the emotional pace of the experience (not arbitrary, tied to brand voice)
5. **Spatial Context** — Show relationships, depth, or hierarchy that's otherwise static

Everything else is noise.

---

## Workflow

### Step 1: Audit the Design for Semantic Opportunities

Read the design (HTML/CSS/reference) and ask:

- **What information hierarchy needs emphasis?** Where should eyes land first?
- **What interactions exist?** Buttons, form inputs, scrollable sections, toggles?
- **What's the brand tone?** Luxury (slow, restrained) vs. energetic (quicker, snappier)?
- **What information is hidden initially?** Cards below the fold? Form steps? Nested details?
- **What's the aesthetic?** Brutalist/minimal designs *reject* motion. Editorial designs might benefit from it. Neumorphic designs can use motion to reinforce tactile depth.

### Step 2: Map Semantic Purposes to Motion

For each opportunity, assign one semantic purpose. Examples:

| Opportunity | Purpose | Motion Type | Example |
|---|---|---|---|
| CTA button in hero | Attention + Feedback | Hover state + subtle scale/glow | `scaleX: 1.05` on hover, reduced-motion: disable |
| Form field validation | Feedback | Color shift + shake on error | Red outline + `rotateX: 2deg` oscillate, success → green check reveal |
| Product grid below fold | Information Reveal | Stagger entrance on scroll | Each card `opacity: 0 → 1` + slight `y` offset, triggered by intersection observer |
| Luxury brand heading | Tempo / Brand Rhythm | Measured entrance | Text reveal line-by-line, 200ms stagger, `ease-out` |
| Card surface (neumorphic/bento) | Spatial Context | Lift on hover | Subtle shadow expand + `translateY(-2px)` to show "floating" |

**Rule**: If you can't name the semantic purpose in one sentence, don't add the animation.

### Step 3: Implement with Restraint

Use **GSAP** for smooth, zero-latency animations. Key principles:

- **Entrance animations**: Stagger, fade + translate. Keep it under 600ms (faster = more energetic, slower = more premium).
- **Scroll animations**: Use `ScrollTrigger` with `scrub: 0` for interactive responsiveness. Avoid heavy parallax unless it *reveals* information.
- **Hover states**: Instant feedback (0-100ms). No delays that make interaction feel sluggish.
- **Micro-interactions**: Form validation, button feedback. Keep these snappy (100-300ms).
- **Reduced motion**: Always detect `prefers-reduced-motion` and disable animations for those users (or replace with instant state changes).

### Step 4: Avoid These Patterns

- **Infinite loops** on non-interactive elements (spinning logos, floating text, ambient "motion for motion's sake")
- **Sliding containers** that don't reveal new information
- **Overlapping animations** that create visual chaos (choose one focal point per interaction)
- **Easing that fights the message** (bouncy easing on a solemn product; slow ease-in on a quick, energetic brand)
- **Animations that trigger on load** for elements already visible (they're not revealing anything, just wasting time)

### Step 5: Code Pattern

```javascript
// Semantic entrance: cards appear as user scrolls to them
gsap.registerPlugin(ScrollTrigger);

document.querySelectorAll('.card').forEach((card, i) => {
  gsap.from(card, {
    opacity: 0,
    y: 20,
    duration: 0.6,
    delay: i * 0.1,
    scrollTrigger: {
      trigger: card,
      start: 'top 80%',
      once: true // Only animate on first entrance
    }
  });
});

// Semantic hover: button gives feedback
const btn = document.querySelector('.cta');
btn.addEventListener('mouseenter', () => {
  gsap.to(btn, { scale: 1.05, duration: 0.2 });
});
btn.addEventListener('mouseleave', () => {
  gsap.to(btn, { scale: 1, duration: 0.2 });
});

// Reduced motion: respect user preference
const prefersNoMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
if (prefersNoMotion) {
  gsap.globalTimeline.timeScale(0); // Or disable specific animations
  // Better: set duration to 0 for specific animations
  gsap.set('.animated-element', { opacity: 1, y: 0 }); // Instant state
}
```

### Step 6: Match Aesthetic

Different design systems have different animation *personalities*:

- **Brutalist / Minimal**: No animation, or only on interactive elements. Favor instant feedback over entrance animations.
- **Neumorphic / Skeuomorphic**: Subtle hover lift, surface depth cues. Tactile feedback (scale, shadow).
- **Glassmorphism / Modern**: Smooth opacity transitions on hover/entrance. Parallax OK if it reveals depth.
- **Editorial / Grid**: Staggered grid entrances, text reveals. Slower, more deliberate (600-800ms).
- **Bento / Card-based**: Per-card entrance with stagger. Subtle hover lift to show interactivity.

---

## Output

Deliver modified HTML/code with:

1. **GSAP + ScrollTrigger** loaded from CDN (or bundled, depending on context)
2. **Semantic animations only** — each with a purpose you can name
3. **Reduced-motion handling** — always included
4. **Comments explaining why** — each animation block has a one-line semantic justification
5. **No decorative motion** — nothing moves without a reason

If the design is already highly visual or kinetic, you may recommend *removing* animations that feel gratuitous, not adding more.

---

## Examples

### Example 1: Hero + CTA
**Semantic Purpose**: Attention to CTA + Feedback on interaction

```javascript
// Hero heading: measured entrance sets tone
gsap.from('.hero-heading', {
  opacity: 0,
  y: 10,
  duration: 0.8,
  ease: 'power2.out',
  delay: 0.2
});

// CTA button: hover feedback confirms interactivity
const cta = document.querySelector('.cta-button');
cta.addEventListener('mouseenter', () => {
  gsap.to(cta, {
    scale: 1.08,
    boxShadow: '0 8px 24px rgba(0, 0, 0, 0.2)',
    duration: 0.2
  });
});
cta.addEventListener('mouseleave', () => {
  gsap.to(cta, { scale: 1, boxShadow: '0 2px 8px rgba(0, 0, 0, 0.1)', duration: 0.2 });
});
```

### Example 2: Product Grid (Below Fold)
**Semantic Purpose**: Information Reveal as user scrolls

```javascript
gsap.utils.toArray('.product-card').forEach((card, i) => {
  gsap.from(card, {
    opacity: 0,
    y: 30,
    duration: 0.6,
    delay: i * 0.08,
    scrollTrigger: {
      trigger: card,
      start: 'top 85%',
      once: true
    }
  });
});
```

### Example 3: Form Validation
**Semantic Purpose**: Feedback on user action

```javascript
const input = document.querySelector('input[type="email"]');
input.addEventListener('blur', () => {
  if (!isValidEmail(input.value)) {
    gsap.to(input, {
      borderColor: '#e53e3e',
      duration: 0.3
    });
    // Shake animation for stronger feedback
    gsap.fromTo(input, 
      { x: -3 },
      { x: 3, duration: 0.1, repeat: 2, yoyo: true }
    );
  } else {
    gsap.to(input, { borderColor: '#48bb78', duration: 0.3 });
  }
});
```

---

## When NOT to Use This Skill

- If the design already has animations and you're just refining them (use judgment calls inline)
- If the brief is "make it more beautiful" without specifying motion (ask first)
- If the design explicitly rejects motion (brutalist, minimal, accessibility-first) — respect that choice

---

## Key Takeaway

**Semantic animation is lazy animation.** It's the opposite of trying to make everything move. Add motion only when it answers the question: "Does this move make the design clearer, more usable, or more emotionally aligned with the brand?"

If the answer is no, don't animate it.