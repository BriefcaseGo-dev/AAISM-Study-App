# BriefcaseGo — v10 Design System

Reference notes for applying the current site design (marketing site: briefcasego.com) to other BriefcaseGo products, starting with the AI Security Management Prep App. Written after redesigning and shipping the marketing site — use this to brief Claude Code (or me, in chat) on any future visual work.

## Color palette

```css
:root{
  --bg:#142422;        /* page background — dark charcoal-teal */
  --panel:#1B2F2C;      /* nav, cards, panels */
  --panel-2:#223734;    /* nested/secondary panels, pills, tag backgrounds */
  --bone:#E9E4D3;       /* primary text */
  --bone-dim:#B3B3A6;   /* secondary/muted text */
  --rust:#E2431F;       /* headline emphasis (<em> accents in H1/H2) */
  --rust-dim:#8A3D22;
  --amber:#D9A441;      /* primary action color — eyebrows, CTAs, highlights */
  --teal:#74C8AE;       /* positive feedback, "what's next" accents */
  --blue:#7FA3D4;       /* trust signal — spec values, select prose emphasis */
  --maroon:#AC2227;     /* decorative only — never text, fails contrast as foreground */
  --hairline:rgba(233,228,211,0.14);
  --hairline-strong:rgba(233,228,211,0.24);
}
```

**Color rules (don't break these):**
- Amber = primary action/highlight. Eyebrows, dots, primary buttons, price emphasis.
- Rust = headline emphasis only (the `<em>` word in a big heading).
- Teal = positive/correct feedback (quiz correct answers) and "what's coming next" roadmap accents.
- Blue = trust signal — spec sheet values, and occasional emphasis inside body prose (e.g. product name mentioned in running text).
- Maroon = decorative border accent only (e.g. top border on a CTA band). It fails WCAG contrast as text — never use it for readable copy.

## Typography

- **Space Grotesk** (400/500/600/700) — all headings and display type
- **Inter** (400/500/600) — body text
- **JetBrains Mono** (400/500/600/700) — eyebrows, labels, buttons, all-caps mono UI chrome. Needs the 700 weight loaded or bold buttons render synthetic-bold.
- **DM Sans** (900) — logo wordmark only

Google Fonts import used site-wide:
```
family=Space+Grotesk:wght@400;500;600;700&family=Inter:wght@400;500;600&family=DM+Sans:wght@900&family=JetBrains+Mono:wght@400;500;600;700
```

## Architecture conventions

- **One shared stylesheet** (`assets/css/style.css`) — no per-page inline `<style>` blocks. Every page links to it.
- **Nav markup**: a bare `<nav>` (not wrapped in `<header>`), containing `.nav-logo` (logo link), `.nav-toggle` (mobile hamburger button), `.nav-links` (plain `<a>` tabs, pushed to the right edge via `margin-left:auto`). No persistent CTA button in nav — removed deliberately (see decisions below).
- **Footer**: `.footer-links` row + two `<p>` tags (brand line, legal disclaimer). No logo image in the footer.
- **Internal links** use root-relative absolute paths (`/`, `/about/`, `/apps/aaism/`) — this works because the site has a custom domain via `CNAME`, not a GitHub Pages subpath.
- **Asset paths** (CSS, images, favicons) are relative to each page's actual folder depth.
- **Scroll-reveal**: IntersectionObserver adds a `.visible` (or `.reveal-in`) class to `.reveal`-flagged elements as they enter the viewport; CSS handles the opacity/translateY transition. Respects `prefers-reduced-motion`.
- **Progress/gauge bars**: rendered at 0% width, animate up to their real percentage (via a `data-fill` attribute) only once scrolled into view — not on page load.
- **Ticker/marquee** (used above the AAISM hero's prerequisite banner): CSS-only infinite scroll — duplicate the content set once, animate `translateX(0)` → `translateX(-50%)`, pause on hover, edge-fade via `mask-image`.

## Content/copy conventions

- Canonical product name everywhere (nav, footer, most copy): **"AI Security Management Prep"** — not the shorter "AAISM™ Study App."
- "AAISM™" (with trademark symbol) is used when referring to the certification/exam itself, not the product.
- CTA copy patterns: "Explore the App — $9.99" (bottom of articles), "Explore AI Security Management Prep" (no price, used on About/Insights listing), "Get the App — $9.99" / "View the App — $9.99" (product-specific pages).
- Legal/trademark disclaimer text is preserved verbatim across every page — don't paraphrase it.

## Decisions worth knowing the reasoning behind

- **No nav CTA button.** Every page already has strong contextual CTAs (hero, mid-page, bottom band). A persistent nav button hardcoded to one product's name/price doesn't scale once more apps (Music, Soccer, General Trivia) ship, and duplicates the adjacent nav-links tab pointing to the same page. Revisit only once there's a real multi-product hub page to point a generic "Apps" button at.
- **Shared stylesheet over inline styles.** Every page pulls from one CSS file so a future palette or component tweak propagates everywhere instead of needing per-page edits.

## Applying this to the AI Security Management Prep App

The app is a separate product (its own PWA-style web app with license-key unlock, quiz/study-guide/glossary logic) — none of that functional code should change. What should change is the visual chrome to match this system:

- Swap any old color scheme for the v10 palette above.
- Match typography (Space Grotesk / Inter / JetBrains Mono).
- Match component patterns where they overlap — cards, buttons, progress bars, accordions — the AAISM marketing page (`/apps/aaism/`) already has working, styled versions of a quiz UI, accordion, and progress-fill bars that are good visual references even though the real app's functionality is more extensive.
- Keep all app logic, data, and license/unlock flow untouched — this is a reskin, not a rebuild.

## Recommended workflow: chat vs. Claude Code

**Design decisions — do them in chat with me first.** This is exactly how the marketing site redesign worked: I built mockups, we iterated on layout/color/copy visually without touching any real code, and only once a direction was locked did the real implementation happen. If you're not sure yet what the AAISM app's screens should look like in v10, or want to see options side by side, start here — especially useful since I can look at screenshots of the current app and mock up alternatives before anything real is touched.

**Implementation — hand it to Claude Code once direction is set.** Claude Code is already wired into your local repo and git remote and can make real edits and commit them directly. Once we've agreed on the look in chat, the fastest path is bringing this document (and any mockups) to a Claude Code session and having it apply the change directly to the app's codebase.

**How to brief Claude Code with this document:**
1. Drop this file into the app's repo — ideally as `CLAUDE.md` at the repo root. Claude Code automatically reads a `CLAUDE.md` file as persistent project context at the start of every session in that repo, so you won't need to re-paste this each time.
2. If you'd rather not rename it, just reference it explicitly: *"Read DESIGN-SYSTEM.md in this repo and apply the v10 palette and component patterns to [specific screen/page], without touching any app logic."*
3. Be specific about scope per session (e.g. "just the quiz screen" or "just the color palette and buttons") rather than "redesign the whole app" in one shot — smaller, reviewable diffs are easier to sanity-check before committing.

**One important flag, given what just happened:** before Claude Code makes any changes or commits, confirm you're pointing it at the correct, up-to-date clone. We hit exactly this problem on the marketing site — a Windows-side clone and a WSL clone had diverged, with different git remotes. Run `git remote -v` and `git log -1` in whatever folder Claude Code is about to work in, and confirm that matches what's actually deployed, before making changes.
