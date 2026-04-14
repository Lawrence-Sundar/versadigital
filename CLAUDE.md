# Website — Session Learnings

### Website — One-Page Marketing Site
- **Typography pairing** — Cormorant Garamond (weights 300/400/500) for all headlines + Inter for all body, nav and labels. This combination defines the premium consultancy tone. Load both from Google Fonts. Apply Cormorant to headlines with `font-weight:300` — the lightness is what makes it elegant, not heavy
- **Section alternation** — dark (`#0A0A0A`) and light (`#F8F8F8`) sections alternate deliberately. Hero, Observation, How It Works, Founder, Final CTA = dark. Problem, Solution, What You Get, FAQ = light. This creates rhythm without needing decorative elements
- **Gold accent system** — `#C9A84C` as the single accent colour. Used for: section labels (11px, uppercase, 0.15em letter-spacing), card dashes, step numbers, flow diagram connectors, checkmarks, FAQ arrows, CTA buttons, hover states. `#B8933B` as the hover variant. Pair with `rgba(201,168,76,0.25)` box-shadow for the CTA glow effect
- **Scroll-shrink nav** — listen to `window.scroll` (passive), toggle a `.scrolled` class on the nav element, use CSS `transition` on `height` to animate smoothly. Nav goes 68px → 54px. Backdrop blur stays constant; only height changes
- **Intersection Observer for fade-up** — use `threshold: 0.12` so elements animate when 12% is visible, not on entry. Set `opacity:0; transform:translateY(30px)` as default, add `.visible` to trigger. Use `delay-1` through `delay-6` utility classes for staggered children. Call `observer.unobserve(e.target)` after triggering so the animation only fires once
- **FAQ accordion with smooth height** — set `max-height:0; overflow:hidden; transition:max-height 0.4s ease` on the answer div. On open, set `max-height` to `answer.scrollHeight + 'px'`. On close, set back to `'0'`. Never use `display:none` — it can't animate. Rotate the `+` arrow 45° via CSS `transform:rotate(45deg)` to turn it into `×`
- **HTML/CSS flow diagram** — no SVG library needed for a simple vertical flow. Stack `.flow-node` divs with a `.flow-connector` div between each. The connector uses `::after` with a thin gradient line (`linear-gradient(to bottom, gold, transparent)`). A downward arrow glyph (&#9660;) adds direction. Split output nodes with a CSS grid inside the last connector group
- **Scroll indicator** — purely CSS. An `::after` pseudo-element with `width:1px; height:40px` and a `scaleY` + `opacity` keyframe animation. No JS needed. Position absolute at the bottom of the hero
- **Mobile hamburger overlay** — full viewport overlay (`position:fixed; inset:0`) with `display:none` toggled to `display:flex` via a class. Use a large Cormorant Garamond serif font (32px, weight 300) for the nav links inside — this makes the mobile overlay feel editorial rather than functional. Lock `document.body.style.overflow = 'hidden'` when open; restore on close
- **Smooth scroll offset for sticky nav** — `a[href^="#"]` click handler: prevent default, calculate `target.offsetTop - nav.offsetHeight - 16`, pass to `window.scrollTo({behavior:'smooth'})`. Never use CSS `scroll-behavior:smooth` alone when there's a sticky nav — it won't account for the nav height offset
- **Folder structure** — `website/index.html` is a standalone marketing file. Completely separate from the internal tools (`tracker-app/`, `clinic-qualifier/`, `reply-analyser/`). No shared code between them

### Website — Logo SVG + GitHub Pages Deploy
- **Large SVG files exceed the Read tool limit** — `logo.svg` is ~36k tokens and cannot be read with the Read tool. Use `head -c 500 file.svg` via Bash to inspect the opening `<svg>` tag, and `sed -i` to make targeted attribute replacements. Never try to Read or Edit a large SVG directly
- **SVG viewBox tuning is iterative** — cropping an exported SVG to show only part of the artwork is done by adjusting `viewBox="x y width height"`. Expect multiple rounds. Also update the `width` and `height` attributes on the `<svg>` tag itself to match the new viewport dimensions, otherwise the rendered size will be wrong
- **Inline style overrides CSS class** — the nav logo `<img>` has both a `class="nav-logo-img"` and a `style="..."` attribute. The inline style always wins. When tuning logo size, edit the inline style on the element, not just the CSS class — or remove the inline style and control everything from the class
- **Cloudflare obfuscates mailto links at the CDN layer** — the source file can contain a plain `<a href="mailto:...">` and it will still appear obfuscated in the live page if Cloudflare's email obfuscation is enabled. The fix is in the Cloudflare dashboard (Speed → Optimization → disable Email Address Obfuscation), not in the source HTML
- **GitHub Pages deploy from a subfolder** — to deploy only `website/` as a standalone GitHub Pages site, init a new git repo inside that folder (`git init` + first commit), then push to a dedicated GitHub repo. This creates a nested repo — the outer `Versa-Digital-HQ` repo stops tracking the `website/` folder entirely. This is intentional for an isolated public deploy
- **`.nojekyll` file is required for GitHub Pages** — place an empty `.nojekyll` file at the root of the deployed folder to prevent GitHub from running Jekyll processing. Without it, files starting with `_` are ignored and build errors can occur
- **GitHub Pages URL pattern** — after pushing to a repo and enabling Pages (Settings → Pages → Deploy from branch → main / root), the site is live at `https://USERNAME.github.io/REPO-NAME/`. Takes ~1 minute to propagate on first deploy

### Website — Two-Button Hero Pattern + New Section Insertion

- **Two-button hero: wrap in `.hero-btns` div, `fade-up` on the wrapper only** — when adding a primary + secondary CTA to a centered hero, wrap both in a `display:flex; align-items:center; gap:16px; justify-content:center; flex-wrap:wrap` container. Apply the `fade-up delay-3` class to the wrapper div, not the individual buttons. The IntersectionObserver picks up the wrapper and both buttons animate together as a unit
- **`.btn-outline` for dark-background secondary CTAs** — `background:transparent; border:1px solid rgba(255,255,255,0.2); color:rgba(240,240,240,0.75)`. Hover: `border-color:rgba(255,255,255,0.5); color:var(--text-dark)`. Do NOT use this class on light-background sections — it becomes invisible. Create a variant if needed
- **Section alternation maintenance when inserting two new sections** — when inserting two sections into an existing dark/light alternation sequence, order them new-dark then new-light. The existing section after the insertion point resumes the correct alternation naturally. Think about what colour both new sections are before writing the HTML — don't default to dark for both
- **`flex-direction:column` + `flex:1` on card description text** — when two pricing/feature cards have different description lengths, setting `flex:1` on the `.pricing-desc` paragraph pushes the CTA button to the bottom of both cards regardless of content height. Requires `display:flex; flex-direction:column` on the card itself
- **Comment block as `old_string` anchor for section insertion** — when inserting new HTML sections before an existing section, use `<!-- SECTION NAME -->` as the `old_string`. Replace it with `[new sections]\n\n  <!-- SECTION NAME -->`. This is unambiguous, requires no surrounding context lines, and survives reformatting

### Responsive Video Embed Pattern (Loom, YouTube, Vimeo)
- **`padding-bottom: 62.5%` approximates Loom's aspect ratio with chrome** — pure 16:9 would be 56.25%, but Loom's share-embed player adds ~6% of height for its controls bar. 62.5% accommodates the chrome without cropping. Use 56.25% for YouTube/Vimeo embeds (they size the chrome inside the 16:9 box)
- **Standard responsive video container pattern:**
  ```css
  .embed { position: relative; padding-bottom: 62.5%; height: 0; overflow: hidden; }
  .embed iframe { position: absolute; top: 0; left: 0; width: 100%; height: 100%; border: 0; }
  ```
  Works across all viewport widths, maintains aspect ratio, iframe fills the box. Wrap in a max-width container if the page has a wider content area than the video should span
- **Two sequential dark sections need a visible separator** — when inserting a new dark section directly above another dark section, the page blurs into one undifferentiated block. Solution: `border-top: 1px solid var(--border-dark)` on the second section. Alternation or a thin hairline restores visual hierarchy
- **Scroll anchor for secondary CTA** — when a video section offers "prefer to explore yourself?" as a secondary path, link to the next section by its ID (`href="#narrativeHeader"`). The site's existing smooth-scroll handler picks it up. Do not build a separate JS scroller — reuse the existing one

## Builds Log

### 2026-03-26 — versadigital.co pilot offer + pricing + hero CTA
- **Problem solved:** Site had no pilot offer, no pricing section, and a generic hero CTA that did not reflect the founding partner positioning
- **Key pattern used:** Two-button hero (flex wrapper + `.btn-outline` ghost secondary); new dark/light section pair inserted mid-page to maintain alternation; `flex:1` on card descriptions to bottom-align CTAs
- **Reuse potential:** Yes — two-button hero pattern applies any time the primary offer changes and a secondary demo/info CTA is needed; pricing card pattern applies to any two-tier offering
- **Lessons:** Read the full HTML structure before inserting sections — knowing the current alternation sequence prevents colour collisions. For section insertion, always target the `<!-- COMMENT -->` of the next existing section as the `old_string` anchor; it is uniquely matchable with no risk of false positives

### 2026-04-14 — Loom walkthrough embed at top of /demo
- **Problem solved:** Demo page previously led with the narrative "It's 9:47pm..." interactive walkthrough. After recording the 2-minute Loom, it needed to lead with the video (low-friction primary) and keep the interactive demo as a secondary explore-yourself option.
- **Key pattern used:** New `#loomWalkthrough` dark section inserted above existing `#narrativeHeader` via HTML comment anchor. Responsive 62.5% padding-bottom aspect wrapper. `narrativeHeader` updated from "Live Demo" to "Interactive Demo" label and padding reduced so it reads as secondary to the video above. Scroll anchor link from Loom section to narrative section for secondary CTA. Both sections dark — added `border-top` hairline between them to preserve hierarchy.
- **Reuse potential:** Yes — the responsive video embed pattern is reusable anywhere a Loom/YouTube/Vimeo needs to land above existing content. Comment-anchor section insertion continues to be the safest edit pattern for this single-file site.
- **Lessons:** Two dark sections adjacent need a separator or they blur into one block — a 1px border is enough. Loom embed URL pattern is `loom.com/embed/{id}` (for iframes) vs `loom.com/share/{id}` (for email/DM links) — both needed from the same ID for different channels.
