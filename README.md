# xd-qin.github.io

The published GitHub Pages site at **https://xd-qin.github.io**.

This repository holds the **pre-built static output** of the
[`astro-photo-folio`](https://github.com/xd-qin/astro-photo-folio) project — a
minimal Astro photography portfolio and blog. There is no application source
here; everything in this repo is compiled output (HTML, CSS, optimized images
under `_astro/`, `sitemap`, `robots.txt`, etc.). Edit the source in the
`astro-photo-folio` repo, then rebuild and redeploy here.

## How it's served

GitHub Pages serves this as a user site. The deployment model is
**"Deploy from a branch" → root (`/`)**. Two details make the static build work:

- **`.nojekyll`** — disables Jekyll so Pages serves the `_astro/` asset
  directory (Jekyll otherwise hides underscore-prefixed paths, leaving the page
  unstyled and imageless).
- **`site` baked to `https://xd-qin.github.io`** at build time, so canonical
  URLs, the sitemap, Open Graph tags, and JSON-LD all resolve to this domain.

## Rebuild & redeploy

Run from a checkout where `astro-photo-folio` and `xd-qin.github.io` sit
side by side. This pins the production URL **without modifying the
`astro-photo-folio` project** (it uses a throwaway build config that is deleted
afterward):

```bash
# 1. Build the source project for this domain
cd astro-photo-folio
npm ci
cat > astro.config.deploy.mjs <<'CFG'
import base from './astro.config.mjs';
export default { ...base, site: 'https://xd-qin.github.io' };
CFG
npx astro build --config ./astro.config.deploy.mjs
node scripts/strip-unreferenced-assets.mjs   # second half of `npm run build`
rm astro.config.deploy.mjs                    # keep astro-photo-folio pristine

# 2. Replace the build output with the fresh build, keeping this repo's own
#    docs (README.md, LICENSE) — they are not part of the Astro build
cd ../xd-qin.github.io
git rm -rf . >/dev/null
cp -a ../astro-photo-folio/dist/. .
git checkout HEAD -- README.md LICENSE   # restore the docs wiped above
touch .nojekyll

# 3. Publish
git add -A
git commit -m "Redeploy astro-photo-folio"
git push
```

> Note: step 2 wipes all tracked files, so `README.md` and `LICENSE` (which are
> not produced by the Astro build) are restored from git right after copying the
> build in.

## Customizing the site

The deployed site currently uses the template's placeholder content (name,
sample galleries, social links). To personalize it, edit `src/config.ts` and
the image folders in the `astro-photo-folio` repo, then rebuild and redeploy
with the steps above.

## License

Released under the [MIT License](./LICENSE).
