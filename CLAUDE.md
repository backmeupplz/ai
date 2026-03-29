# AI — Better or Worse?

Single-page opinion site at **ai.borodutch.com** weighing arguments for and against AI making everything better.

## Stack

- Pure HTML/CSS/JS in `index.html` — no frameworks, no build step
- Fonts: Sora (headings/body) + IBM Plex Mono (labels/tags)
- Dark theme with muted red (against) and green (for) tints

## How it works

Arguments live in the `ARGUMENTS` object in the `<script>` tag at the bottom of `index.html`. Each argument has:

```js
{ title: "Short title", body: "1-2 sentence description.", weight: 1|2|3 }
```

- `weight: 1` = minor point
- `weight: 2` = solid argument
- `weight: 3` = fundamental

The top bar percentage uses **Bayesian updating**:
- Prior: 50/50 odds (P = 0.5)
- Each argument updates the odds via a likelihood ratio: weight 1 → LR 1.5, weight 2 → LR 3, weight 3 → LR 6
- "For" arguments multiply odds, "against" arguments divide
- Final probability = odds / (1 + odds)

Equal weights on both sides = 50/50. Unbalanced arguments shift the posterior non-linearly (stronger arguments have exponentially more impact).

## Common tasks

- **Add an argument**: Add an entry to `ARGUMENTS.against` or `ARGUMENTS.for` in the script. Pick an appropriate weight. The bar auto-rebalances.
- **Remove an argument**: Delete the entry from the array.
- **Change the bar position**: Adjust weights on existing arguments or add/remove arguments.
- **Style changes**: All CSS is in the `<style>` tag in the same file.
- **Update OG image**: After changing arguments, update `og.svg` bar position/percentage/marker to match the new Bayesian result, then regenerate: `rsvg-convert og.svg -w 1200 -h 630 -o og.png`

## Deployment

- **Repo**: https://github.com/backmeupplz/ai (public)
- **Hosting**: GitHub Pages from `master` branch root
- **Domain**: `ai.borodutch.com` — CNAME file in repo, Cloudflare DNS (zone `1f2511a68b81a60b7280ebbb3c61291d`, CNAME record `ai` → `backmeupplz.github.io`, proxied)
- **Deploy**: Just push to `master`. GitHub Pages rebuilds automatically.
