# push-hub landing page

Pre-launch landing page for push-hub, used for demand validation. Plain static HTML and CSS: no build step, no JavaScript, no external fonts or trackers.

```text
lp/
├── index.html        # English (default)
├── ja/index.html     # Japanese
└── assets/
    ├── styles.css    # shared styles (black / green-cyan)
    └── favicon.svg
```

Copy follows the repository [README](../README.md). When the README changes, update both `index.html` and `ja/index.html`.

## Local preview

```sh
python3 -m http.server -d lp 8000
# http://localhost:8000/     (English)
# http://localhost:8000/ja/  (Japanese)
```

All paths are relative, so you can also open `lp/index.html` directly in a browser. Relative paths also keep the page working under the GitHub Pages project path (`/push-hub/`).

## Deploy (GitHub Pages)

The page is deployed by [`.github/workflows/pages.yml`](../.github/workflows/pages.yml), which uploads the `lp/` directory to GitHub Pages.

* **Trigger:** pushes to `main` that change `lp/**` or the workflow, or a manual run (**Actions → Deploy landing page → Run workflow**).
* **URL:** `https://aianchor-lab.github.io/push-hub/` until a custom domain is set.

One-time setup: **Settings → Pages → Build and deployment → Source: GitHub Actions**.

### Custom domain (optional)

1. Buy the domain and add DNS records at the registrar: a `CNAME` record pointing a subdomain (e.g. `www`) to `aianchor-lab.github.io`, or `A` / `AAAA` records for the apex domain (see the [GitHub docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)).
2. Set the domain in **Settings → Pages → Custom domain** and enable **Enforce HTTPS**. With a GitHub Actions deployment, no `CNAME` file is needed.

## TODO

* Replace the pre-order placeholder (`#preorder`) with the real button and click tracking (#16).
* Once the production domain is fixed, make the `hreflang` links absolute and add `og:url` / `og:image`.
