# util.80rian.com

Personal utility collection deployed at [util.80rian.com](https://util.80rian.com).

## Utilities

| Path | Description |
|------|-------------|
| [`/reels-cover`](https://util.80rian.com/reels-cover/) | Instagram Reels cover image generator |

## Deploy

Cloudflare Pages via `wrangler`:

```bash
wrangler pages deploy . --project-name util-80rian
```

## Adding a New Utility

1. Create a directory: `mkdir new-util`
2. Add `new-util/index.html`
3. Add entry to root `index.html`
4. Deploy
