# Frontier Model Governance

A six-page interactive explainer. Open `Model Governance.dc.html` in a browser; it needs no build step and no server.

## Pages

1. **The layers** — a cutaway sphere of the seven layers around a model. Click a band in the cut, or a label, to pull that layer out. The chips above filter the diagram to the layers a given question touches.
2. **Two documents** — the internal safety framework versus the public compliance framework, plus who holds the authority.
3. **The process** — the seven-step loop, the five criteria, what gets tested, the two risk levels, and the decision chain.
4. **The testing** — the same request sent down the product path and the evaluation path, side by side.
5. **The gap** — where provider responsibility ends and the deploying company's begins.
6. **Glossary** — the seven terms the rest of the material assumes.

Arrow keys move between pages; the top nav jumps directly.

## Files

```
Model Governance.dc.html    the whole design — markup, logic and content
support.js                  runtime
_ds/nocturne-.../           design tokens and component styles
```

All content lives in `Model Governance.dc.html`. The layer copy, glossary, gaps and testing formats are plain arrays at the top of the logic class, so text edits do not require touching markup.

## Colors

EY yellow (`#ffe600`) on EY greys, set as CSS custom properties in the `:root` block at the top of the file. Changing that block re-themes every page.

## Note

The copy is vendor- and client-neutral: no AI provider, cloud platform, consultancy or individual is named. Statutes (the EU AI Act, California's Transparency in Frontier AI Act) are named, since they are public law.

## Hosting on Azure

This is a static site — one HTML file, one JS file, and a folder of design assets. No build step, no server-side code. **Azure Static Web Apps** is the right fit: it serves static content over a global CDN with free TLS, and the free tier covers a site this size at no cost.

### Option A — deploy from GitHub (recommended)

Static Web Apps' native workflow watches a GitHub repo and redeploys on every push, via a GitHub Actions workflow it creates for you.

```bash
# one-time: create the resource, linked to this GitHub repo
az staticwebapp create \
  --name frontier-model-governance \
  --resource-group <your-resource-group> \
  --source https://github.com/<your-org>/model-governance-interactive \
  --location "eastus2" \
  --branch main \
  --app-location "/" \
  --output-location "/" \
  --login-with-github
```

This provisions the Static Web App, wires up a GitHub Actions workflow in `.github/workflows/`, and gives you a `*.azurestaticapps.net` URL immediately. Because `--app-location` and `--output-location` are both the repo root, no build command is needed — files are served as-is.

### Option B — deploy directly from this folder (no GitHub link)

Useful for a one-off push or a private demo, without wiring CI.

```bash
# one-time: create the app shell (no source repo attached)
az staticwebapp create \
  --name frontier-model-governance \
  --resource-group <your-resource-group> \
  --location "eastus2"

# get a deployment token
az staticwebapp secrets list \
  --name frontier-model-governance \
  --query "properties.apiKey" -o tsv

# deploy with the Static Web Apps CLI (npx, no global install needed)
npx @azure/static-web-apps-cli deploy . \
  --deployment-token <token-from-above> \
  --env production
```

### Custom domain

```bash
az staticwebapp hostname set \
  --name frontier-model-governance \
  --hostname governance.yourdomain.com
```

Then add the CNAME record it prints to your DNS provider — Azure issues and manages the TLS certificate automatically once the record resolves.

### Notes

- The Phosphor Icons stylesheet is loaded from `unpkg.com` (see the `<link>` in `Model Governance.dc.html`). If the hosting environment blocks outbound calls to third-party CDNs, vendor that stylesheet locally instead.
- No environment variables, API keys, or backend config are required — the whole site is client-side.
