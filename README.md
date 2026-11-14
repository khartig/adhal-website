# adhal website

The landing page for [adhal](https://github.com/khartig/adhal): direct,
end-to-end encrypted, peer-to-peer file drops.

It is a static site with no build step and no trackers. Tailwind (Play CDN),
Lucide icons and Geist fonts load from CDNs; everything else is inline in
`index.html`.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole page: markup, styles and scripts |
| `og-image.png` | 1200x630 card shown when the URL is shared on social media |
| `llms.txt` | Plain-text summary of adhal for AI tools and assistants |
| `robots.txt` | Lets search engines and AI crawlers index the site |
| `.nojekyll` | Tells GitHub Pages to serve the files as they are |

## What's on the page

- **Make the connection (hero):** an interactive demo. Visitors drag the sample
  file onto "Your device", or press "Try a sample transfer", and watch a drop
  play through five stages: Pair, Find, Seal, Transfer, Verify. Each stage can
  be selected to explain what really happens and who can see what. The demo
  only ever uses the built-in sample file; it never asks for, reads or sends a
  visitor's own files.
- **Send a file:** in the nav, under the demo, after each run and at the
  bottom of the page. There is no browser client, so it opens a dialog with
  the real CLI commands.
- **Today, it's two commands:** a terminal replay of a real CLI drop, looping
  with a 5-second pause.
- **The shift:** Direct, Sealed, Unbound.
- **Under the hood:** a spec list. The dotted terms (HyperDHT, Hyperswarm,
  NAT, CPace PAKE, ChaCha20-Poly1305, SHA-256) open a short definition dialog.
- **Roadmap** and **footer** (© 2026 Nebarti, LLC).

The visual vocabulary is deliberately small: charcoal surfaces, one cyan
accent, and one signature motion, a line that draws itself between two points.
It is used for the connection in the demo, the stage timers, the roadmap rail
and the section dividers. All motion respects `prefers-reduced-motion`.

Every claim in the demo and the spec list should match the adhal repository
(README, `docs/PROTOCOL.md`, `docs/THREAT-MODEL.md`). In particular, the page
says "no server stores your file", not "no server": DHT nodes see the meeting
point and IP addresses, and the relay is opt-in and experimental.

## Run locally

```bash
open index.html            # or, to serve it like a real site:
python3 -m http.server 8080   # then http://localhost:8080
```

## Deploy

Any static host works: Netlify, Vercel, Cloudflare Pages, GitHub Pages. Upload
**all** the files above, including `.nojekyll`, to the same folder.

**Serve the site from the root of its domain** (`https://example.com/`), not
from a subpath. Crawlers only look for `https://example.com/robots.txt` and
`https://example.com/llms.txt`. A GitHub Pages *project* site
(`user.github.io/repo/`) is a subpath, so give it a custom domain, or use a
user site (`user.github.io`).

After deploying, check that both files load as plain text:

```bash
curl -I https://<your-domain>/robots.txt   # expect 200, text/plain
curl -I https://<your-domain>/llms.txt     # expect 200, text/plain
```

Once the site has a domain:

- In `<head>` of `index.html`, change `og:image`, `twitter:image` and `og:url`
  to absolute URLs (`https://<your-domain>/og-image.png`). Most social
  platforms ignore relative ones. Check with a link-preview debugger such as
  opengraph.xyz.
- Optionally add `Sitemap: https://<your-domain>/sitemap.xml` to `robots.txt`
  if you publish a sitemap.

For production, swap the Tailwind Play CDN for a compiled stylesheet. The CDN
build compiles in the browser and logs a warning in the console:

```bash
npx tailwindcss@3 -i input.css -o styles.css --content index.html --minify
```

`input.css` holds the three `@tailwind` directives. Move the `tailwind.config`
block from `<head>` into `tailwind.config.js`, then replace the CDN `<script>`
with `<link rel="stylesheet" href="styles.css">`.

## Customize

All of these live in `index.html`.

- **Colors and fonts:** the `tailwind.config` block in `<head>` (`ink` for
  surfaces, `accent` for cyan).
- **Demo stages:** the `STAGES` array in the script: each stage's label,
  duration, title, body and "who sees what" line.
- **Demo layout:** `LAYOUTS.wide` (side by side) and `LAYOUTS.narrow` (top to
  bottom, used when the stage is under 680px wide) hold every position in the
  scene, including where the sample file and its guide line sit.
- **Sample file:** `SAMPLE` (name, size, fingerprint), and the matching text
  in the `#sampleFile` button.
- **Glossary:** the `TERMS` object. To make another word clickable, wrap it in
  `<button type="button" class="term" data-term="key">` and add `key` to
  `TERMS`.
- **Terminal replay:** the `SCRIPT` array. Its lines mirror real output from
  `cli/src/swarm-receive.js` and `swarm-send.js`, so update them when the
  CLI's output changes. `REPLAY_GAP_MS` sets the pause between replays.
- **Send dialog:** `#sendDialog` holds the CLI commands.
- **Roadmap:** one `<article>` per phase in `#roadmap`. Phase 2 has dashed
  placeholders for future download buttons.
- **Search and AI discovery:** the JSON-LD block in `<head>`, `llms.txt` and
  `robots.txt`. Keep them in step with the page when facts change.
