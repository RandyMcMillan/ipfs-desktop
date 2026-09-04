# Deploying the WebUI to GitHub Pages

The IPFS WebUI bundled in `assets/webui/` is a static React application. It can be deployed to GitHub Pages and accessed directly from a browser, independent of the Electron desktop wrapper.

## Enabling GitHub Pages

1. Go to **Settings → Pages** in your repository.
2. Under **Build and deployment**, set **Source** to **GitHub Actions**.
3. Push the `.github/workflows/gh-pages.yml` workflow to the default branch, then trigger it manually from the **Actions** tab or by pushing a change to `assets/webui/`.

The site will be available at `https://<username>.github.io/<repo-name>/`.

## CORS Configuration

When the WebUI is served from `github.io`, the browser treats it as a cross-origin request relative to the Kubo API (typically `http://127.0.0.1:5001`). You must configure CORS headers on your Kubo node or the WebUI will be unable to connect.

Run the following commands on the machine running the IPFS daemon, replacing `<origin>` with your GitHub Pages URL (e.g. `https://myuser.github.io`):

```bash
ipfs config --json API.HTTPHeaders.Access-Control-Allow-Origin '["<origin>"]'
ipfs config --json API.HTTPHeaders.Access-Control-Allow-Methods '["PUT", "POST", "GET"]'
```

Restart the daemon for the changes to take effect:

```bash
ipfs daemon
```

If you need to allow multiple origins, include them in the array:

```bash
ipfs config --json API.HTTPHeaders.Access-Control-Allow-Origin '["https://myuser.github.io", "https://webui.ipfs.io"]'
```

> **Warning:** Do not use `["*"]` with credentials-enabled requests. The WebUI may send authenticated requests that fail when `Access-Control-Allow-Origin` is a wildcard.

## Limitations vs. Desktop

Features that rely on Electron APIs are unavailable in the browser build:

- Desktop-specific analytics (Countly)
- Language sync via IPC
- "Open in external browser" handling
- Automatic API address injection

Core functionality — files, pins, peers, status, and configuration — works as long as the Kubo API is reachable and CORS is configured.
