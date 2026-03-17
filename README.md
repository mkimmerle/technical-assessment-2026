# Best Sellers Carousel — Technical Assessment

A responsive "Best Sellers" product carousel section built as part of a Shopify theme developer technical assessment.

## What was built

**`index.html`** — A self-contained static implementation using vanilla JavaScript and Tailwind CSS (via CDN). Includes:
- Horizontally scrollable product carousel on desktop with a custom draggable scrollbar
- 2-column product grid on mobile with a "Show More" button that reveals additional products and removes itself on click
- Product cards with primary/secondary image swap on hover, "Best Seller" and "Save X%" badges, star ratings, and pricing
- Fully responsive layout driven by Tailwind breakpoint utilities (`md:` prefix), with a single set of card markup serving both layouts

**`best-sellers.liquid`** — A Shopify section file that ports the static implementation into Liquid. Includes:
- A merchant-configurable collection picker, max products setting (1–10), "Shop All" toggle, and star ratings toggle
- Server-side rendering of all product data via Shopify's Liquid templating engine
- Section instance-scoped element IDs using `{{ section.id }}` to prevent conflicts when the section is added to a page more than once
- Internationalisation-ready strings via `| t` translation filters

**`snippets/product-reviews-stars.liquid`** — A placeholder snippet for third-party reviews app implementation (Judge.me, Stamped, Yotpo, Okendo, Loox).

## Technical decisions and tradeoffs

**Tailwind via CDN vs. build pipeline** — The static file loads Tailwind from the CDN for simplicity. In a production Shopify theme this would be replaced with a proper build step and `tailwind.config.js`, where custom fonts (`Source Serif 4`, `Bebas Neue`, `Poppins`) would be registered as named font family utilities rather than defined in a `<style>` block.

**Custom scrollbar** — The native browser scrollbar is hidden via `::-webkit-scrollbar` (Chrome/Safari) and `scrollbar-width: none` (Firefox), replaced with a custom-built thumb that reflects scroll position, responds to hover, and is draggable. Thumb width is calculated dynamically as a ratio of visible width to total scroll width, matching native scrollbar behavior.

**Image hover swap** — The secondary image is stacked absolutely behind the primary and crossfades on hover using Tailwind's `group` and `group-hover:` utilities. Both images are present in the DOM on load, which means the secondary image begins loading immediately — avoiding the flash that would occur with a JS-driven `src` swap on `mouseenter`.

**Star ratings** — Since Shopify deprecated native Product Reviews in 2024 the ratings display is controlled by a theme editor toggle and isolated to a placeholer snippet (`product-reviews-stars.liquid`) that gives a brief example of how it might work with common third-party apps.

**Lazy loading** — Product card images currently do not have a `loading="lazy"` attribute because the carousel is the only content on the page. If the carousel is implemented on a real page and is below the fold, lazy-loading should be used.

**`prefers-reduced-motion`** — All transitions and animations are wrapped in `motion-safe:` Tailwind utilities or `@media (prefers-reduced-motion: no-preference)` blocks in CSS, ensuring the experience degrades gracefully for users who have requested reduced motion at the OS level.
