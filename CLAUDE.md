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
- Each argument updates the odds via a likelihood ratio: weight 1 → LR 1.2, weight 2 → LR 1.5, weight 3 → LR 2
- "For" arguments multiply odds, "against" arguments divide
- Final probability = odds / (1 + odds)

Equal weights on both sides = 50/50. Unbalanced arguments shift the posterior non-linearly (stronger arguments have exponentially more impact).

## Common tasks

- **Add an argument**: Add an entry to `ARGUMENTS.against` or `ARGUMENTS.for` in the script. Pick an appropriate weight. Place it among other arguments of the same weight (weight 3 first, then 2, then 1). **Always recalculate and update the OG image after adding/removing/reweighting arguments** (see below).
- **Remove an argument**: Delete the entry from the array. **Always recalculate and update the OG image** (see below).
- **Change the bar position**: Adjust weights on existing arguments or add/remove arguments.
- **Style changes**: All CSS is in the `<style>` tag in the same file.
- **Update OG image** (REQUIRED after any argument change):
  1. Calculate the new Bayesian posterior by extracting all weights from `ARGUMENTS.for` and `ARGUMENTS.against`, using `LR_MAP` from the script (`{ 1: 1.2, 2: 1.5, 3: 2 }`).
  2. Update `og.svg`: set the marker `cx` to `150 + (pctWorse / 100) * 900`, and update the percentage text to `"pctWorse / pctBetter"`.
  3. Regenerate the PNG: `rsvg-convert og.svg -w 1200 -h 630 -o og.png`

## Deployment

- **Repo**: https://github.com/backmeupplz/ai (public)
- **Hosting**: GitHub Pages from `master` branch root
- **Domain**: `ai.borodutch.com` — CNAME file in repo, Cloudflare DNS (zone `1f2511a68b81a60b7280ebbb3c61291d`, CNAME record `ai` → `backmeupplz.github.io`, proxied)
- **Deploy**: Just push to `master`. GitHub Pages rebuilds automatically.
