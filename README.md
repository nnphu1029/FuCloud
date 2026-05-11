# FuCloud/FileBrowser Web
A lightweight static web page used to redirect users to a dynamic Cloudflare TryCloudflare tunnel.

## Purpose

This repository hosts a simple `index.html` page via GitHub Pages.

The page automatically redirects visitors to the current temporary Cloudflare tunnel URL.

## How It Works

1. A local service is exposed using `cloudflared tunnel --url`.
2. Cloudflare generates a temporary `trycloudflare.com` URL.
3. The generated URL is updated inside `index.html`.
4. GitHub Pages serves the redirect page publicly.

## Structure

```text
index.html
README.md
```

## Example Redirect

```html
<meta http-equiv="refresh" content="0; url=https://example.trycloudflare.com">
```

## Deployment

Hosted using GitHub Pages.

## Notes

- `trycloudflare.com` URLs are temporary.
- The redirect target changes every time a new tunnel is created.
