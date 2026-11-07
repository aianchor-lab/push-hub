# push-hub landing page

Pre-launch landing page for push-hub, used for demand validation. Plain static HTML and CSS: no build step, no JavaScript, no external fonts or trackers.

```text
lp/
├── index.html        # English (default)
├── ja/index.html     # Japanese
├── assets/
│   ├── styles.css    # shared styles (light / dark)
│   └── favicon.svg
└── vercel.json       # clean URLs and security headers
```

Copy follows the repository [README](../README.md). When the README changes, update both `index.html` and `ja/index.html`.

## Local preview

```sh
python3 -m http.server -d lp 8000
# http://localhost:8000/     (English)
# http://localhost:8000/ja/  (Japanese)
```

All paths are relative, so you can also open `lp/index.html` directly in a browser.

## Deploy to Vercel

1. In Vercel, **Add New → Project** and import `aianchor-lab/push-hub`.
2. Set **Root Directory** to `lp`.
3. Set **Framework Preset** to **Other**. Leave Build Command and Output Directory empty.
4. Deploy. Pushes to `main` deploy to production; pull requests get preview URLs.

## TODO

* Replace the survey URL `https://forms.gle/TODO` with the real Google Form (#14).
* Replace the pre-order placeholder (`#preorder`) with the real button and click tracking (#16).
* Once the production domain is fixed, make the `hreflang` links absolute and add `og:url` / `og:image`.
