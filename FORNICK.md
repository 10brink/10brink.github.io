# FORNICK.md - Understanding Your Personal Website

Hey Nick! This is the story of how your personal website works, written so you can learn from it long after you've forgotten the details. Think of this as a letter from past-you to future-you.

---

## The Big Picture

You built something that professional developers charge thousands of dollars for: a personal website with a photo gallery, social links, and an interactive fireworks animation. And you did it without React, without npm, without build tools, without any of the complexity that makes modern web development feel intimidating.

Your site is what we call a **static site**. Imagine the difference between a vending machine and a restaurant. A restaurant has a chef who cooks your meal on demand (that's a dynamic site with a server). A vending machine just hands you pre-made items (that's your static site). GitHub Pages acts as your vending machine host - it just serves your HTML files to anyone who asks.

---

## How the Pieces Connect

```
index.html (The Front Door)
    │
    ├── socials.html ──► External links (Mixcloud, SoundCloud, X, Instagram)
    │
    ├── personal.html ──► family-photos.html (password-protected)
    │
    ├── projects.html ──► fireworks/fireworks.html
    │
    └── External: GitHub, LinkedIn, Airbnb
```

Every page shares one CSS file (`style.css`). This is crucial. When you change a color or font size in one place, it updates everywhere. This is called the **DRY principle** - Don't Repeat Yourself. If you find yourself copy-pasting the same CSS into multiple files, you're doing it wrong.

---

## The Tech Decisions (And Why They Matter)

### Why No Frameworks?

You could have used React, Vue, Next.js, or any of the trendy tools. Here's why vanilla HTML/CSS/JS was the right call:

1. **No build step** - You edit a file, refresh the browser, see the change. No waiting for Webpack to compile. No mysterious build errors at 2am.

2. **No dependencies to break** - JavaScript packages get abandoned, have security vulnerabilities, or make breaking changes. Your site will work in 10 years because it relies on nothing but web standards.

3. **Instant load times** - Your entire site is probably under 100KB. React alone is 40KB gzipped before you write a single line of code.

4. **You actually understand it** - Every line of code is yours. No magic happening behind the scenes.

The trade-off? If this site grew to 50 pages, you'd want a templating system to avoid repeating the header/footer on every page. But for a personal site, vanilla is perfect.

### Why Inline SVG Icons?

You'll notice the social icons are embedded directly in the HTML as `<svg>` elements, not loaded as image files. Three reasons:

1. **No extra HTTP requests** - Each `<img>` is a round trip to the server. Inline SVG loads with the page.

2. **CSS styling** - Notice how the icons change color on hover? You can style SVG with CSS using `fill` and `stroke`. Can't do that with PNG.

3. **Crisp at any size** - SVGs are vector graphics. They look perfect on a Retina display or a 4K monitor.

### The Background Trick

Look at this line in your CSS:

```css
background:
  linear-gradient(180deg, rgba(5, 15, 20, 0.7) 0%, rgba(10, 25, 30, 0.75) 100%),
  url('img/pichu.jpg') center/cover no-repeat fixed;
```

This is two backgrounds stacked: a dark gradient overlay ON TOP OF your image. The gradient has transparency (that's what `rgba` means - the last number is opacity). This lets the photo show through while keeping text readable. Without the overlay, light parts of your photo would make white text invisible.

The `fixed` keyword is subtle but important - it means the background doesn't scroll with the page, creating a subtle depth effect.

---

## The Fireworks: A Physics Lesson

The fireworks animation is where things get interesting. It's essentially a tiny physics engine running in your browser.

### The Game Loop

Games and animations use something called a **game loop** - code that runs 60 times per second (matching your screen's refresh rate). Here's the pattern:

```javascript
function animate() {
    // 1. Clear (or fade) the screen
    // 2. Update all object positions
    // 3. Check for collisions
    // 4. Draw everything
    // 5. Do it again
    requestAnimationFrame(animate);
}
```

`requestAnimationFrame` is the magic function. It tells the browser "run this function right before the next screen refresh." It's more efficient than `setInterval` because it pauses when the tab is hidden.

### The Particle System

Each firework dot is a `Particle` object. When you click, you don't create one particle - you create 8-64 of them in a pattern (circle, heart, star, diamond). Each particle has:

- Position (x, y)
- Velocity (vx, vy) - how fast it's moving and in what direction
- Gravity effect (vy increases each frame, pulling it down)
- A target position (where it should expand to before falling)

This is exactly how professional game engines work, just simplified.

### Collision Detection

The code checks if any two particles are overlapping:

```javascript
const dx = other.x - this.x;
const dy = other.y - this.y;
const distance = Math.sqrt(dx * dx + dy * dy);
```

This is the Pythagorean theorem from high school! The distance between two points. If that distance is less than the sum of their radii, they're touching.

When particles collide, they can trigger chain reactions - spawning smaller particles. This is emergent complexity: simple rules creating interesting behavior.

### The Bar Chart

The bars at the bottom count particles in each vertical "column" of the screen. But they don't just show raw counts - they use logarithmic scaling:

```javascript
const logScale = Math.log2(smoothedCounts[i] + 1) / Math.log2(FIXED_MAX_COUNT + 1);
```

Why logarithmic? Human perception is logarithmic. The difference between 1 and 10 particles feels bigger than between 100 and 109, even though both are +9. Log scaling makes the visualization match how you *feel* the particle density.

---

## The Password Protection (And Its Limits)

Your family photos page has a password gate. Let's be honest about what it does and doesn't do:

**What it does:**
- Prevents casual visitors from seeing photos
- Stores authentication in `sessionStorage` so you don't re-enter it every page load

**What it doesn't do:**
- Actually secure the photos. Anyone who looks at the source code can see the password is `family2024`. Anyone who opens Developer Tools can see the hidden photo URLs.

For a family album shared with a few relatives, this is fine. For anything truly sensitive, you'd need server-side authentication. But that would require a backend, hosting costs, complexity. For your use case, "security through obscurity" plus the fact that nobody's looking for your photos is sufficient.

---

## Lessons for Future Projects

### 1. Start Simple, Add Complexity When Needed

Your site started as a single page. You added pages when you needed them. You didn't plan a whole architecture upfront. This is the right approach for personal projects.

### 2. Shared Styles Prevent Chaos

One `style.css` file means consistent design. When you wanted to change the gold accent color, you changed it in one place. Imagine if every page had its own copy of the styles - you'd have 6 different shades of gold within a month.

### 3. Semantic HTML Matters

You used proper tags: `<main>`, `<section>`, `<article>`, `<nav>`. Not just `<div>` everywhere. This helps screen readers, search engines, and future-you understanding the code.

### 4. Mobile-First Is Real

Your CSS uses `@media (max-width: 480px)` to adjust for phones. The photo grid switches from 3 columns to 2 on small screens. This isn't optional anymore - more than half of web traffic is mobile.

### 5. External Links Get Special Treatment

Every external link has `target="_blank" rel="noopener"`. The `noopener` is a security measure - without it, the linked page can access your page's `window.opener` object. It's a small thing, but good engineers think about these details.

---

## Common Pitfalls to Avoid

### The Z-Index Trap

If you ever add something that needs to layer on top of something else (like a modal or tooltip), don't just slap `z-index: 9999` on it. That leads to z-index wars. Create a scale: modals at 100, tooltips at 50, dropdowns at 25. Document it.

### Image Optimization

Your family photos are probably several MB each. On slow connections, this hurts. In the future, consider:
- Resizing images to max 1920px wide
- Using WebP format (30% smaller than JPEG)
- Lazy loading (you already do this with `loading="lazy"`)

### Color Contrast

Your gold text (#d4a574) on dark backgrounds looks good, but barely passes accessibility guidelines. If you ever use it on a lighter background, it becomes unreadable. Test contrast at webaim.org/resources/contrastchecker.

### The Single Point of Failure

Right now, your entire site is on GitHub. If GitHub goes down, your site goes down. If your account gets hacked, everything's gone. For a personal site this is acceptable risk, but for anything important: have backups, consider a custom domain, maybe use two hosts.

---

## How Good Engineers Think

Building this site, you practiced several engineering mindsets:

1. **Iterative development** - You didn't design everything upfront. You built, tested, adjusted.

2. **Understanding trade-offs** - No framework = simplicity but less scalability. You made the right choice for the scale.

3. **Reading code as much as writing it** - Understanding how the fireworks physics worked meant reading through the particle class carefully.

4. **Debugging systematically** - When something didn't work, you isolated the problem, checked the browser console, tested hypotheses.

5. **Knowing when to stop** - Your site works. It could always be "better," but shipping beats perfecting.

---

## What's Next?

If you want to level up from here:

- **Add a dark/light mode toggle** - Learn about CSS custom properties and `prefers-color-scheme`
- **Make a blog that doesn't need code to update** - Look into static site generators like Hugo or 11ty
- **Host on a custom domain** - Learn about DNS, CNAME records, SSL certificates
- **Add analytics** - Understand who visits your site (Plausible is privacy-friendly)

But honestly? This site does its job. Sometimes the best next step is to build something completely different.

---

*Built with Claude Code - January 2026*
