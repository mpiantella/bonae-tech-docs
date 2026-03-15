# Static Site Frameworks for South American SMBs

## Context & Constraints

Building sites for South American small businesses introduces specific constraints that shape framework choice: variable internet connectivity (especially outside major metros), cost sensitivity for hosting, need for fast page loads on mid-range mobile devices, multilingual support (Spanish/Portuguese), and the ability to produce polished sites quickly with a small team.

This guide evaluates frameworks beyond Hugo and Astro (assumed known), focusing on practical fit for this market.

---

## Framework Landscape

### 1. Eleventy (11ty)

**What it is:** A zero-config, JavaScript-based static site generator that's deliberately simple. No client-side JS by default — it outputs pure HTML/CSS.

**Architecture:** Template-driven. Supports Nunjucks, Liquid, Markdown, Handlebars, and more. Data cascade system lets you layer global, directory, and file-level data. No framework lock-in — your output is just HTML.

**UXD patterns:** Content-first design. Because there's zero JS bundle by default, you're pushed toward progressive enhancement — add interactivity only where needed. This naturally produces accessible, fast-loading sites that work on low-bandwidth connections.

**Pros:**
- Near-zero learning curve if you know HTML/CSS — no framework abstractions to learn
- Fastest build times of any SSG (seconds, not minutes)
- Zero client JS = perfect Lighthouse scores out of the box
- Extremely lightweight output — ideal for Venezuelan mobile networks
- Template language flexibility (use what you already know)
- Easy i18n through data files and directory structure

**Cons:**
- No built-in component model — you manage partials manually
- Styling is BYO (no design system included)
- Ecosystem is smaller than Next.js or Gatsby — fewer plugins
- Image optimization requires manual setup or plugins
- Less "wow factor" in developer experience compared to newer tools

**Data points:**

| Metric | Rating |
|--------|--------|
| Ease to learn | ★★★★★ (if you know HTML) |
| Dev time for a 5-page site | 1–2 days |
| Deployment complexity | Minimal — static files anywhere |
| Bundle size | 0 KB JS (unless you add it) |

---

### 2. Next.js (Static Export Mode)

**What it is:** React's dominant meta-framework. While known for SSR, its `output: 'export'` mode generates fully static sites. You get React's component model with static output.

**Architecture:** React-based with file-system routing. App Router (recommended) or Pages Router. Static export pre-renders all pages at build time. API routes are NOT available in static mode — external services (Supabase, etc.) handle dynamic needs.

**UXD patterns:** Component-driven UI. React's ecosystem gives access to design systems like shadcn/ui, Radix, and Chakra UI, which follow modern UXD standards including WAI-ARIA accessibility, responsive design tokens, and consistent spacing/typography systems. Supports layout patterns (shared navs, footers) natively.

**Pros:**
- Massive ecosystem — component libraries, UI kits, templates
- If the team grows, the same codebase can evolve into SSR/ISR without rewrite
- shadcn/ui + Tailwind = professional-looking sites fast
- Image optimization with next/image (though limited in static export)
- Strong TypeScript support
- Easy path from static to dynamic if a client outgrows a static site

**Cons:**
- React overhead — even static pages ship a JS runtime (~80-120KB gzipped)
- Learning curve is steeper than template-based SSGs
- Build times are slower than Hugo or 11ty
- Static export mode has limitations (no middleware, no API routes, no ISR)
- Overkill for a simple brochure site

**Data points:**

| Metric | Rating |
|--------|--------|
| Ease to learn | ★★★☆☆ (React knowledge required) |
| Dev time for a 5-page site | 2–3 days |
| Deployment complexity | Low — Vercel, Netlify, or any CDN |
| Bundle size | 80–150 KB JS baseline |

---

### 3. SvelteKit

**What it is:** Svelte's official application framework. Can generate fully static sites via `@sveltejs/adapter-static`. Svelte compiles components to vanilla JS at build time — no virtual DOM, no runtime framework.

**Architecture:** Component-based with file-system routing. Svelte's compiler approach means components are compiled away, leaving only the minimal JS needed for actual interactivity. Layouts, error pages, and loading states are first-class concepts.

**UXD patterns:** Svelte's reactivity model makes animations, transitions, and micro-interactions trivial — `transition:fade`, `animate:flip` are built-in. This enables polished UX without heavy animation libraries. Skeleton UI and DaisyUI provide Svelte-native design systems. Built-in accessibility warnings in the compiler.

**Pros:**
- Smallest JS bundles of any component framework — great for bandwidth-constrained users
- Compiler catches accessibility issues at build time
- Built-in transitions and animations = polished feel with less effort
- Intuitive syntax — closest to writing plain HTML/CSS/JS
- Faster to learn than React or Vue for someone coming from vanilla web dev
- Strong SEO defaults in static mode

**Cons:**
- Smaller ecosystem than React — fewer pre-built components and templates
- Fewer developers in the LATAM market know Svelte (hiring consideration)
- Some enterprise clients may question "non-standard" tech choice
- Less mature than Next.js for complex use cases
- Fewer ready-made templates/themes available

**Data points:**

| Metric | Rating |
|--------|--------|
| Ease to learn | ★★★★☆ (intuitive if you know HTML/JS) |
| Dev time for a 5-page site | 1.5–2.5 days |
| Deployment complexity | Low — static adapter outputs files for any CDN |
| Bundle size | 5–30 KB JS (only what's needed) |

---

### 4. Nuxt (Static Mode)

**What it is:** Vue's meta-framework, analogous to Next.js for React. `nuxt generate` produces a fully static site. Since you know Vue, this is the natural static site path.

**Architecture:** Vue 3 + Composition API with file-system routing. Nuxt Content module turns Markdown/YAML into a queryable content layer — ideal for client sites where non-devs need to update content. Auto-imports components and composables.

**UXD patterns:** Vue's ecosystem includes Vuetify, PrimeVue, and Nuxt UI — mature design systems following Material Design or custom design tokens. Nuxt UI in particular provides a modern, accessible component library tailored for Nuxt. NuxtImage handles responsive images and lazy loading automatically.

**Pros:**
- You already know Vue — zero framework learning curve
- Nuxt Content makes content management almost CMS-like without a CMS
- Auto-imports reduce boilerplate significantly
- Strong i18n module (@nuxtjs/i18n) with SEO-friendly locale routing
- Nuxt UI provides beautiful, accessible components out of the box
- Great developer experience with Nuxt DevTools

**Cons:**
- Vue runtime adds JS weight (~30-50KB gzipped)
- Build times are moderate (faster than Gatsby, slower than Hugo)
- Some Nuxt 3 modules are still maturing
- Hydration can cause layout shifts if not careful
- Smaller talent pool than React in general market

**Data points:**

| Metric | Rating |
|--------|--------|
| Ease to learn | ★★★★★ (you know Vue already) |
| Dev time for a 5-page site | 1–2 days |
| Deployment complexity | Low — Netlify/Vercel/static hosting |
| Bundle size | 30–60 KB JS baseline |

---

### 5. Webflow

**What it is:** A visual web design tool that generates production-ready static sites. No code required for standard sites, but supports custom code embeds. Exports clean HTML/CSS/JS.

**Architecture:** Visual canvas → hosted site or exported code. CMS is built-in (visual content management). Hosting is on Webflow's CDN or you export and self-host. Interactions and animations are visual, GSAP-based under the hood.

**UXD patterns:** Webflow's design model is based on the CSS box model — it teaches proper web design thinking. Built-in interactions follow modern motion design principles. Responsive design is visual breakpoint-based. Client Billing lets you manage client sites under your agency account.

**Pros:**
- Fastest path from design to production — no code needed
- Client can update content themselves via CMS (huge for SMBs)
- Professional-quality animations and interactions without code
- Built-in form handling, SEO controls, and responsive design
- Agency/freelancer features (client billing, white-label, transferable sites)
- Extensive template marketplace for quick starts

**Cons:**
- Monthly cost per site ($14-39/month per site, or $23-49 with CMS)
- Vendor lock-in — exporting produces code that's hard to maintain manually
- Custom functionality requires workarounds or third-party integrations
- Limited to what the visual builder supports
- Pricing in USD can be significant for Venezuelan SMB budgets
- Internet required for development (no offline mode)

**Data points:**

| Metric | Rating |
|--------|--------|
| Ease to learn | ★★★★☆ (visual, but CSS model knowledge helps) |
| Dev time for a 5-page site | 0.5–1 day |
| Deployment complexity | None (hosted) or export static files |
| Bundle size | 50–150 KB (depends on interactions) |

---

### 6. Framer

**What it is:** Design-to-production tool evolved from a prototyping tool. Generates static sites with React under the hood. More opinionated and modern-feeling than Webflow.

**Architecture:** Visual editor with component system. Sites are hosted on Framer's infrastructure (Vercel-backed). No code export — you're committed to Framer hosting. CMS is built-in and simpler than Webflow's.

**UXD patterns:** Framer's design model emphasizes modern layout patterns — auto-layout (Flexbox), responsive variants, and scroll-based animations. Pre-built sections follow contemporary landing page patterns. Strong typography and spacing controls.

**Pros:**
- Extremely fast for landing pages and marketing sites
- Beautiful default aesthetics — hard to make an ugly site
- Built-in SEO optimization and analytics
- Simpler learning curve than Webflow
- AI-powered features for content and layout suggestions
- Great for rapid prototyping and client presentations

**Cons:**
- No code export — full vendor lock-in
- Pricing per site ($5-30/month per site)
- Less flexible than Webflow for complex layouts
- Limited CMS compared to Webflow
- Not suitable for sites requiring custom backend logic
- Relatively new — less established agency ecosystem

**Data points:**

| Metric | Rating |
|--------|--------|
| Ease to learn | ★★★★★ (most intuitive visual builder) |
| Dev time for a 5-page site | 3–6 hours |
| Deployment complexity | None (hosted only) |
| Bundle size | 60–120 KB (React-based) |

---

### 7. Bridgetown

**What it is:** A progressive Ruby-based site generator, spiritual successor to Jekyll. Modern architecture with component support via Ruby or web components.

**Architecture:** Ruby + ERB/Liquid templates with optional Roda server for SSR. Component model supports both Ruby components and web components. Built-in Markdown processing with front matter. Integrates with esbuild for JS bundling.

**UXD patterns:** Convention-over-configuration approach. Encourages progressive enhancement — start static, add interactivity via islands. Supports Tailwind CSS out of the box. Layout and partial system for consistent page structures.

**Pros:**
- Clean, modern API with good documentation
- Component model bridges static and interactive needs
- Built-in Tailwind integration
- Good for content-heavy sites (blogs, documentation)
- Active community with modern Ruby practices

**Cons:**
- Ruby dependency — outside your current stack (Go/JS)
- Smaller ecosystem than JS-based alternatives
- Fewer templates and themes available
- Community is passionate but small
- Not the best choice if you're standardizing on JS/Go

**Data points:**

| Metric | Rating |
|--------|--------|
| Ease to learn | ★★★☆☆ (requires Ruby knowledge) |
| Dev time for a 5-page site | 2–3 days |
| Deployment complexity | Low — static output to any CDN |
| Bundle size | 0–20 KB (progressive enhancement) |

---

## Comparative Matrix

| Framework | Learning Curve | Dev Speed | JS Bundle | Client Self-Service | Monthly Cost | Offline Dev | LATAM Fit |
|-----------|---------------|-----------|-----------|-------------------|-------------|------------|-----------|
| 11ty | Very Low | Fast | 0 KB | No (needs CMS) | $0 + hosting | Yes | ★★★★★ |
| Next.js Static | Medium | Medium | 80–150 KB | Via headless CMS | $0 + hosting | Yes | ★★★☆☆ |
| SvelteKit | Low-Med | Fast | 5–30 KB | No (needs CMS) | $0 + hosting | Yes | ★★★★☆ |
| Nuxt Static | Low (for you) | Fast | 30–60 KB | Via Nuxt Content | $0 + hosting | Yes | ★★★★☆ |
| Webflow | Low-Med | Very Fast | 50–150 KB | Yes (built-in) | $14–49/site | No | ★★★☆☆ |
| Framer | Very Low | Fastest | 60–120 KB | Yes (built-in) | $5–30/site | No | ★★☆☆☆ |
| Bridgetown | Medium | Medium | 0–20 KB | No (needs CMS) | $0 + hosting | Yes | ★★☆☆☆ |

---

## Recommendations for Your Context

### For maximum speed with minimum cost:
**Nuxt Static + Nuxt UI + Nuxt Content.** You already know Vue, Nuxt UI gives you polished components instantly, and Nuxt Content lets you structure client content in Markdown files. Pair with Cloudflare Pages (free tier, good LATAM edge nodes) for deployment.

### For the lightest possible output:
**11ty + Tailwind CSS.** Zero JS shipped to the client. Sites load instantly even on 3G connections. The tradeoff is more manual work on interactive elements and no built-in design system — but for simple business sites (restaurant, store, professional services), this produces the fastest possible result.

### For scalability as clients grow:
**Astro** (which you know) is actually the sweet spot here. It supports multiple UI frameworks, ships zero JS by default with opt-in islands, and has a growing content layer. If a client later needs dynamic features, you add them without changing frameworks.

### For non-technical client handoff:
**Webflow**, if the client's budget supports it. The ability for the client to update their own content without calling you is a huge value proposition for SMBs. Build it once, hand it over, charge for maintenance.

### Stack recommendation for the agency:
Standardize on **Astro** as the primary SSG (you know it, it's flexible, minimal JS) with **Nuxt** for sites that need more interactivity. Use **Tailwind CSS** across both for design consistency. Deploy to **Cloudflare Pages** for free hosting with excellent Latin American edge coverage. Add a headless CMS like **Decap CMS** (free, Git-based) when clients need self-service content editing.

# Additional Static Site Frameworks for LATAM Markets

## LATAM-Specific Design Constraints

Before diving into additional frameworks, it's worth making the LATAM constraints explicit, because they filter out tools that work well in North America or Europe but fail in this context:

**Network reality:** Outside Caracas, Bogotá, São Paulo, and other major metros, mobile connections are frequently 3G or unstable 4G. Average page weight tolerance should target under 500KB for initial load. Every KB of JavaScript shipped is a tax on your users.

**Device landscape:** Mid-range Android devices (2-4GB RAM) dominate. Heavy frameworks that assume 8GB+ RAM with fast processors produce janky experiences. Time-to-Interactive (TTI) matters more than Time-to-First-Byte (TTFB).

**Cost structure:** USD-denominated hosting costs are amplified by local currency exchange rates. A $20/month service that seems trivial in the US is a meaningful line item for a Venezuelan SMB. Free tiers and usage-based pricing are critical.

**Offline tolerance:** Users expect sites to at least show *something* when connectivity drops. PWA capabilities (service workers, caching, installability) are a genuine differentiator, not a nice-to-have.

**Mobile-first is non-negotiable:** 70-80%+ of web traffic in LATAM comes from mobile devices. Desktop is secondary.

---

## Additional Frameworks

### 8. Qwik (via Qwik City)

**What it is:** A performance-first framework created by Miško Hevery (the creator of Angular). Qwik's core innovation is "resumability" — instead of hydrating (re-executing all JS on the client), it serializes app state into HTML at build time and only loads JavaScript when users actually interact with something.

**Architecture:** Component-based with JSX/TSX syntax (familiar if you know React). Qwik City is the meta-framework (like Next.js for React) that handles routing, SSG, and SSR. Static site generation produces pure HTML files. JavaScript is loaded in micro-chunks via a background web worker — no main thread blocking.

**UXD patterns:** Qwik enforces progressive enhancement by architecture. The page is fully rendered and readable before any JS loads. Interactions activate on demand. This naturally produces sites that score 95-100 on Lighthouse performance. Supports Tailwind, has growing component ecosystem.

**Why it matters for LATAM:**
- Near-zero initial JS payload (~1KB bootstrapper) regardless of app complexity
- Pages become interactive almost instantly even on 3G
- Users see content immediately — no loading spinners, no layout shifts
- Background prefetching via web worker doesn't block the UI thread

**Tradeoffs:**
- Newer framework — smaller ecosystem and community than React/Vue
- Learning curve around resumability concepts (different mental model from React)
- Fewer pre-built templates and themes
- Hiring developers who know Qwik is harder

**Data points:**

| Metric | Rating |
|--------|--------|
| Ease to learn | ★★★☆☆ (JSX familiar, but resumability is new) |
| Dev time for a 5-page site | 2–3 days |
| Deployment complexity | Low — static output to any CDN |
| Initial JS payload | ~1 KB (then lazy-loads on interaction) |
| TTI on 3G | Excellent — near-instant |

---

### 9. Zola

**What it is:** A Rust-based static site generator distributed as a single binary with zero dependencies. No Node.js, no npm, no Ruby. You download one file and it has everything: Sass compilation, syntax highlighting, search index generation, image resizing, and multilingual support built-in.

**Architecture:** Content in Markdown with TOML front matter. Templates use Tera (similar to Jinja2/Liquid). Taxonomies (tags, categories) are first-class. Builds are measured in milliseconds — a 500-page site builds in under a second.

**UXD patterns:** Template-driven, content-first. Since output is pure HTML/CSS with zero JS, design quality depends entirely on your templates and CSS skills. Pair with Tailwind or a CSS framework for polished results. Built-in table of contents, anchor links, and search index support common content site patterns.

**Why it matters for LATAM:**
- Single binary — no dependency hell, works on any machine
- Zero JS output = absolute minimum bandwidth consumption
- Built-in multilingual support (Spanish/Portuguese) with locale-aware URL routing
- Sub-second builds mean instant feedback during development
- Hosting cost is essentially $0 (GitHub Pages, Cloudflare Pages)

**Tradeoffs:**
- No component model — pure templates and partials
- Tera template language has a learning curve if you're used to JSX
- Fewer themes than Hugo (though quality themes like "tabi" exist)
- No plugin system — features are limited to what's built in
- Interactivity requires manually adding JS

**Data points:**

| Metric | Rating |
|--------|--------|
| Ease to learn | ★★★★☆ (simple concepts, good docs) |
| Dev time for a 5-page site | 1–2 days |
| Deployment complexity | Minimal — single binary, static output |
| Bundle size | 0 KB JS |
| Build speed | Sub-second for most sites |

---

### 10. VitePress

**What it is:** Vue-powered static site generator built on Vite, created by Evan You (Vue's creator). Spiritual successor to VuePress. Ships with a polished default theme optimized for documentation and content sites, but fully customizable for any static site.

**Architecture:** Vue 3 + Vite under the hood. Content written in Markdown with Vue components embeddable directly in Markdown files. Static HTML generated at build, with Vue hydrating only interactive parts. The default theme includes search, dark mode, sidebar navigation, and responsive design out of the box.

**UXD patterns:** The default theme follows documentation-site conventions (sidebar nav, header nav, search), but custom themes can implement any pattern. Since it's Vue-based, all Vue component libraries are available. Built-in internationalization support. Sensible responsive breakpoints in the default theme.

**Why it matters for LATAM:**
- You already know Vue — zero framework learning
- Excellent default theme means professional sites with minimal effort
- Built-in i18n for Spanish/Portuguese content
- Vite's build tooling produces optimized, tree-shaken output
- Perfect for service-oriented SMB sites (menus, catalogs, portfolios)

**Tradeoffs:**
- Default theme is documentation-oriented — business sites need custom theming
- Vue runtime is shipped (though smaller than React)
- Less flexible than Nuxt for complex routing needs
- Focused more on content/docs than general-purpose web apps

**Data points:**

| Metric | Rating |
|--------|--------|
| Ease to learn | ★★★★★ (you know Vue) |
| Dev time for a 5-page site | 0.5–1.5 days |
| Deployment complexity | Minimal — static output |
| Bundle size | 20–40 KB JS |
| Built-in features | Search, dark mode, i18n, responsive |

---

### 11. SolidStart (Static Mode)

**What it is:** The meta-framework for SolidJS. Solid uses fine-grained reactivity (no virtual DOM) and compiles components to highly efficient vanilla JS. SolidStart supports SSR, SSG, and hybrid rendering via Vinxi/Nitro.

**Architecture:** Component-based with JSX syntax. Fine-grained reactivity means only the specific DOM nodes that change get updated — no diffing, no virtual DOM overhead. Pre-rendering generates static HTML. File-system routing. Built on Nitro (same server engine as Nuxt).

**UXD patterns:** SolidJS produces some of the smallest JS bundles of any component framework. Reactivity is automatic and granular. Animations and transitions are smooth because updates are surgical. Growing ecosystem includes SolidUI components. Strong accessibility primitives via Kobalte (headless components).

**Why it matters for LATAM:**
- Smallest runtime of any reactive framework (~7KB)
- Fine-grained updates mean smooth performance on low-end devices
- JSX syntax familiar from React — easier transition than Svelte or Qwik
- Nitro-based server can deploy to edge (Cloudflare Workers, etc.)

**Tradeoffs:**
- Smallest ecosystem of the major frameworks — fewer components, fewer tutorials
- SolidStart is still maturing (docs are still in beta)
- SSG support requires manual route configuration
- Harder to hire for than React or Vue
- Mental model shift from React (looks similar but works differently)

**Data points:**

| Metric | Rating |
|--------|--------|
| Ease to learn | ★★★☆☆ (JSX familiar, but reactivity model is different) |
| Dev time for a 5-page site | 2–3 days |
| Deployment complexity | Low — static adapter for any CDN |
| Bundle size | 7–25 KB JS |
| Runtime performance | Best-in-class |

---

### 12. Lume

**What it is:** A static site generator for Deno (the modern JavaScript/TypeScript runtime). Created by Óscar Otero, it's deliberately simple — inspired by Eleventy but with Deno's advantages: TypeScript-first, secure by default, no `node_modules`.

**Architecture:** Supports multiple template engines (Nunjucks, Markdown, JSX, Eta, Pug). Data model is straightforward — files and folders. Plugins handle common needs (Tailwind, image optimization, sitemap, etc.). Builds are fast. Ships with a dev server with live reload.

**UXD patterns:** Content-first, template-driven. Like 11ty, output is pure HTML/CSS unless you add JS. The Deno ecosystem provides modern tooling. Excellent for content sites, blogs, portfolios, and business pages. Supports MDX for embedding components in Markdown.

**Why it matters for LATAM:**
- Zero JS output by default — minimal bandwidth
- Deno means no dependency vulnerabilities, no `node_modules` bloat
- TypeScript-first development experience
- Simple mental model — easy to train team members
- Deno's recent adoption of `npm:` compatibility means access to Node packages too

**Tradeoffs:**
- Deno ecosystem is smaller than Node — some tools may not be available
- Less community support and fewer tutorials than 11ty or Hugo
- Niche choice — may complicate onboarding new developers
- Not widely known, so client perception may be an issue

**Data points:**

| Metric | Rating |
|--------|--------|
| Ease to learn | ★★★★☆ (simple if you know JS/TS) |
| Dev time for a 5-page site | 1–2 days |
| Deployment complexity | Minimal — static files |
| Bundle size | 0 KB JS (default) |
| Runtime | Deno (TypeScript-first) |

---

### 13. PWA-Enhanced Static Sites (Pattern, not Framework)

**What it is:** Not a framework per se, but a critical architectural pattern for LATAM. Any static site generator can be enhanced with Progressive Web App capabilities: service workers for offline caching, web app manifest for installability, and background sync for form submissions.

**Architecture:** You build the static site with any SSG (Astro, Hugo, 11ty, etc.), then layer on PWA capabilities. Tools like Workbox (from Google) automate service worker generation. VitePWA plugin works with any Vite-based framework (Astro, SvelteKit, Nuxt, SolidStart, Qwik City).

**UXD patterns:** App-like experience from a static site. Users can "install" the site to their home screen (no app store needed). Content loads instantly from cache on repeat visits. Offline fallback page shows cached content when connectivity drops. Push notifications for re-engagement (optional).

**Why it matters for LATAM:**
- Offline access is transformative for unreliable connections
- Installable = no app store fees, no download friction, no 50MB app
- Cached assets mean near-zero data usage on repeat visits
- Starbucks, Twitter Lite, Pinterest all proved PWA value in emerging markets
- OLX India (classifieds, similar model to MercadoLibre) saw massive engagement gains with PWA

**Implementation approach:**
- **With Vite-based frameworks:** Use `vite-plugin-pwa` — automatic service worker generation, manifest creation, and caching strategies
- **With Hugo/Zola/11ty:** Manually add a manifest.json and use Workbox CLI to generate service workers
- **Caching strategy:** Cache-first for static assets (CSS, images, fonts), network-first with cache fallback for HTML pages, stale-while-revalidate for API data

**Data points:**

| Metric | Rating |
|--------|--------|
| Additional dev time | 0.5–1 day to add to existing site |
| Offline capability | Full (cached pages) or partial (fallback page) |
| Install size | ~0 (web-based, no download) |
| Push notification support | Android full, iOS 16.4+ partial |
| User perception | App-like without app store |

---

## Expanded Comparative Matrix

| Framework | Initial JS | 3G Load Time | i18n | Offline PWA | Monthly Cost | Dev Speed | LATAM Fit |
|-----------|-----------|-------------|------|------------|-------------|-----------|-----------|
| Qwik City | ~1 KB | Excellent | Manual | Via plugin | $0 + hosting | Medium | ★★★★★ |
| Zola | 0 KB | Excellent | Built-in | Manual | $0 + hosting | Fast | ★★★★★ |
| VitePress | 20–40 KB | Very Good | Built-in | Via plugin | $0 + hosting | Very Fast | ★★★★☆ |
| SolidStart | 7–25 KB | Excellent | Manual | Via plugin | $0 + hosting | Medium | ★★★★☆ |
| Lume | 0 KB | Excellent | Plugin | Manual | $0 + hosting | Fast | ★★★★☆ |
| + PWA layer | +0 KB | Cached instant | N/A | Yes | +$0 | +0.5 day | ★★★★★ |

*Compared with previous document frameworks:*

| Framework | Initial JS | 3G Load Time | i18n | Offline PWA | Monthly Cost | Dev Speed | LATAM Fit |
|-----------|-----------|-------------|------|------------|-------------|-----------|-----------|
| 11ty | 0 KB | Excellent | Manual | Manual | $0 + hosting | Fast | ★★★★★ |
| Nuxt Static | 30–60 KB | Good | Built-in | Via plugin | $0 + hosting | Fast | ★★★★☆ |
| SvelteKit | 5–30 KB | Very Good | Manual | Via plugin | $0 + hosting | Fast | ★★★★☆ |
| Next.js Static | 80–150 KB | Moderate | Manual | Via plugin | $0 + hosting | Medium | ★★★☆☆ |
| Webflow | 50–150 KB | Moderate | Manual | No | $14–49/site | Very Fast | ★★★☆☆ |
| Framer | 60–120 KB | Moderate | Limited | No | $5–30/site | Fastest | ★★☆☆☆ |

---

## Deployment Strategy for LATAM

Regardless of framework, the hosting choice matters enormously for LATAM performance:

**Cloudflare Pages** (recommended primary): Free tier with unlimited bandwidth. Cloudflare has edge nodes in São Paulo, Buenos Aires, Bogotá, Santiago, Lima, and Mexico City. This means sub-50ms TTFB across most of South America. Free SSL, free custom domains, Git-based deploy.

**GitHub Pages**: Free, reliable, but served from US/EU — higher latency to South American users. Fine for low-traffic sites.

**Vercel**: Free tier, good edge network, but the free tier has bandwidth limits (100GB/month). Good Vercel edge nodes exist in São Paulo and other LATAM locations.

**Netlify**: Similar to Vercel in pricing. Edge CDN has LATAM presence. Free tier includes 100GB bandwidth and 300 build minutes.

**Self-hosted on Hetzner/Contabo + Cloudflare CDN**: A €4-5/month VPS with Cloudflare in front gives you full control. The VPS can be in a US East Coast location (low latency to northern South America via submarine cables) with Cloudflare caching at the edge.

---

## Revised Recommendations

### Tier 1: Best LATAM fit (zero/minimal JS, fastest loads)
- **Zola** — if you want Hugo-like speed with a better template language
- **11ty** — if you want JS ecosystem compatibility with zero-JS output
- **Any of the above + PWA layer** — for offline capability

### Tier 2: Great balance (small JS, component model, scalable)
- **Astro** (you know it) — islands architecture, multi-framework support
- **Qwik City** — best-in-class performance for interactive sites
- **SvelteKit** — smallest bundles of the component frameworks

### Tier 3: Fastest development (your existing skills, good enough performance)
- **Nuxt Static** — your Vue knowledge, Nuxt UI components, Nuxt Content for CMS
- **VitePress** — when you just need a clean content site fast

### The practical agency stack:
For the digital services company, standardize on **Astro + Tailwind** as the default (zero JS, flexible, growing ecosystem). Use **Nuxt** when clients need more interactivity. Add **PWA capabilities via vite-plugin-pwa** to every project by default — it's minimal effort and a genuine differentiator you can sell to clients. Deploy everything to **Cloudflare Pages** for the best LATAM edge performance at $0 cost.

When pitching to clients, the PWA angle is particularly strong: "Your site works even when your customers have bad internet, and they can install it on their phone without downloading anything from an app store." That's a concrete, understandable benefit for a Venezuelan business owner.
