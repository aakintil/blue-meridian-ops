# Blue Meridian Ops — website

A single-page static site for Blue Meridian Ops, the AI automation and workflow
consulting arm of Blue Meridian. One HTML file, no build step, no dependencies
beyond two Google Fonts.

Target domain: `thebluemeridian.co`

## Files

| File | What it is |
|---|---|
| `index.html` | The entire site. Inline CSS, inline JS, inline SVG logo. |
| `logo-white.svg` | The logo as a standalone file, for favicon and OG image work. |

## What the site is

A fixed-viewport layout. Nav, hero and tabs are pinned to the top, the footer is
pinned to the bottom, and the panel between them is the only thing that scrolls.
Four panels swap in place via tabs rather than the page scrolling through
sections.

Panels:

1. **What we do** — positioning and the problem being solved
2. **How we work** — three-step process (audit, build, handoff)
3. **Proof of work** — three case studies as cards with stat rows
4. **Contact** — email and a call booking link

## Design system

```
Background       #0073E6   (WCAG AA, 4.57:1 with white text)
Text             #FFFFFF
Hairlines        rgba(255,255,255,0.16)
Card background  rgba(255,255,255,0.10)  hover 0.14

Display font     Space Grotesk   400/500/600/700
Body font        Neuton          200/300/400/700 + italic
```

Space Grotesk carries headlines, tabs, buttons, labels, stats and the wordmark.
Neuton carries all body copy. The pairing is deliberate: geometric sans for
structure, warm serif for reading.

### On the blue

The background was originally `#1C5DFF`. It moved to `#0073E6` to sit closer to
a reference site the client liked (netilradio.com, `#007EFF`) while still
passing WCAG AA for normal text. `#007EFF` itself measures 3.87:1 and fails.
`#0073E6` is the same 210° hue, 5% darker, and measures 4.57:1.

**If the background colour changes, re-check contrast.** Most of the body copy
on this site sits directly on the background, so a lighter blue breaks
accessibility for the bulk of the page.

## Behaviour worth knowing before editing

**Panel positioning is computed in JS, not CSS.** Each `.panel` is
`position: fixed` with its `top` set at runtime from
`#top-fixed`'s measured height. This is driven by `setTopOffset()`, which runs
on load, on resize, on orientation change, after `document.fonts.ready`, and
through a `ResizeObserver` on the header.

The fonts hook matters. Neuton and Space Grotesk change the hero height when
they load, and an earlier version had panels starting in the wrong place
because the offset was measured before that happened. Don't replace this with a
hardcoded CSS value.

**Which panels scroll.** On desktop only `#panel-what` and `#panel-work` carry
`.scrollable`; the other two are short enough to centre without scrolling. On
mobile every panel scrolls except `#panel-contact`. Scrollable panels use a CSS
`mask-image` to fade content out above the footer rather than cutting it off.

**Tabs.** Roving tabindex, arrow keys plus Home and End move between them, and
deep links work: `#what`, `#how`, `#work`, `#contact` each open their panel
directly and push to history. `IDS` in the script must stay in the same order as
the buttons in the DOM.

**Focus styling.** Links get a white outline. Tabs deliberately do not: they
show keyboard focus by activating their existing underline, because a second
box around an already-underlined tab looked wrong.

**Motion.** One animation only, a staggered rise on the hero contents at load.
Everything else is a transition on hover or tab change. All of it is disabled
under `prefers-reduced-motion`. Previous versions had a canvas animation and a
Three.js globe in the hero; both were removed deliberately. Don't reintroduce
ambient motion.

## Open items

- [ ] **`Book a call` link is a dead `#`.** In `#panel-contact`. Needs the real
      Calendly or Cal.com URL.
- [ ] **Favicon.** Generate from `logo-white.svg`. Needs a coloured background
      since the logo is white-on-transparent and would vanish on a light
      browser tab.
- [ ] **OG image.** The meta tags point at `https://thebluemeridian.co/og.png`
      and that file does not exist yet. Until it does, link previews in
      WhatsApp and Slack render blank. 1200x630.
- [ ] **Verify one stat.** "2 platforms aligned" on the National Platform case
      was inferred from a contractors-and-homeowners split in the source deck.
      Confirm before launch.

## Hosting

Static single file, so any static host works. Recommendation is **Cloudflare
Pages** over GitHub Pages: same free tier, but Cloudflare has edge presence in
Lagos and Johannesburg and GitHub does not. The primary audience is Nigeria, so
that is a real latency difference.

Deploy is: push the repo, connect it in the Cloudflare dashboard, set build
command to none and output directory to root, then add `thebluemeridian.co` as
a custom domain.

GitHub Pages also works if preferred: `index.html` at repo root, enable Pages in
Settings, add a `CNAME` file containing `thebluemeridian.co`, point DNS at
GitHub's four A records.

## Context

Blue Meridian Ops is one of two arms. The other, Blue Meridian Studio (brand and
design), lives at `bmeridian.studio` and has a different audience. They were
deliberately kept apart: there is no cross-link between the sites, and a footer
link to the Studio was added and then removed on purpose.

Primary market is Nigeria. The US and UK case studies are on the site as
credibility signals rather than because those are target markets.
