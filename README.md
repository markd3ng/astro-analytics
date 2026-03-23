# Astro Analytics 📈

_A collection of Astro components for popular web analytics tools._

## Supported services

- `Fathom`
- `GoogleAnalytics`
- `Metrical`
- `Plausible`
- `SimpleAnalytics`
- `Umami`
- `Amplitude`
- `Matomo`
- `Clarity`
- `MinimalAnalytics` _(deprecated, Universal Analytics only)_

## Installation

```bash
npm install astro-analytics
# or
pnpm add astro-analytics
# or
yarn add astro-analytics
```

## Quick start

```astro
---
import { Plausible } from 'astro-analytics';

const isProduction = import.meta.env.PROD;
---

<html lang="en">
  <head>
    {isProduction && <Plausible domain="example.com" />}
  </head>
  <body>
    <slot />
  </body>
</html>
```

## Recommended Astro integration

### 1. Inject analytics once in a shared layout

For most Astro sites, place your analytics component in a global layout such as `src/layouts/BaseLayout.astro` or a shared `Head.astro` partial. This avoids duplicate script injection and duplicate pageviews.

```astro
---
import {
  Clarity,
  GoogleAnalytics,
  Plausible,
} from 'astro-analytics';

const isProduction = import.meta.env.PROD;
---

<html lang="en">
  <head>
    {isProduction && <GoogleAnalytics id="G-XXXXXXXXXX" />}
    {isProduction && <Plausible domain="example.com" />}
    {isProduction && <Clarity id="abcd1234xy" />}
  </head>
  <body>
    <slot />
  </body>
</html>
```

### 2. Only enable analytics in production

Use `import.meta.env.PROD` or your own environment flag to avoid polluting analytics dashboards during local development and preview work.

### 3. If you use Astro client-side navigation, verify pageview behavior

If your project uses `ClientRouter`, View Transitions, or other SPA-like navigation, some providers may require extra configuration or manual pageview calls. This package focuses on script injection; it does not add a provider-specific Astro router adapter.

### 4. Gate analytics on consent when required

If your site uses a consent banner, conditionally render analytics components only after consent is granted.

```astro
---
import { Umami } from 'astro-analytics';

const hasConsent = true;
---

{hasConsent && <Umami id="e676c9b4-11e4-4ef1-a4d7-87001773e9f2" src="https://analytics.example.com/umami.js" />}
```

## Component reference

### `Fathom`

Loads the Fathom script with your site ID.

```astro
<Fathom site="ABCDEF" />
<Fathom site="ABCDEF" src="https://cdn.usefathom.com/script.js" />
```

Props:
- `site` (`string`, required): Fathom site ID.
- `src` (`string`, optional): Override the script URL for self-hosting or a custom CDN. Defaults to `https://cdn.usefathom.com/script.js`.

### `GoogleAnalytics`

Embeds the Google tag (`gtag.js`) for GA4.

```astro
<GoogleAnalytics id="G-XXXXXXXXXX" />
```

Props:
- `id` (`string`, required): GA4 measurement ID such as `G-XXXXXXXXXX`.

### `Metrical`

Embeds the Metrical tracker.

```astro
<Metrical app="j5gZ1K26a" />
```

Props:
- `app` (`string`, required): Metrical app ID.

### `Plausible`

Embeds the Plausible script. The default script URL now uses the current official `plausible.js` path.

```astro
<Plausible domain="example.com" />
<Plausible domain="example.com" src="https://analytics.example.com/js/plausible.js" />
```

Props:
- `domain` (`string`, required): The domain to associate with tracked pageviews.
- `src` (`string`, optional): Override the script URL. Defaults to `https://plausible.io/js/plausible.js`.

### `SimpleAnalytics`

Embeds the Simple Analytics script.

```astro
<SimpleAnalytics />
```

Props:
- No props.

### `Umami`

Embeds the Umami tracker with the current recommended `id` and `src` API, while still supporting the legacy `dataId` + `site` + `host` props for compatibility.

Recommended usage:

```astro
<Umami
  id="e676c9b4-11e4-4ef1-a4d7-87001773e9f2"
  src="https://analytics.example.com/umami.js"
/>
```

Optional host and domain controls:

```astro
<Umami
  id="e676c9b4-11e4-4ef1-a4d7-87001773e9f2"
  src="https://analytics.example.com/umami.js"
  dataHostUrl="https://analytics.example.com/"
  domains={["example.com", "www.example.com"]}
/>
```

Legacy-compatible usage:

```astro
<Umami
  dataId="e676c9b4-11e4-4ef1-a4d7-87001773e9f2"
  site="https://analytics.example.com"
/>
```

Props:
- `id` (`string`, optional): Recommended website ID prop.
- `src` (`string`, optional): Recommended full script URL, for example `https://analytics.example.com/umami.js`.
- `dataId` (`string`, optional): Legacy alias for `id`.
- `site` (`string`, optional): Legacy base site URL used together with `host`.
- `host` (`string`, optional): Legacy script path appended to `site`. Defaults to `/umami.js`.
- `dataHostUrl` (`string`, optional): Umami host URL passed to `data-host-url`.
- `domains` (`string | string[]`, optional): Domain allowlist passed to `data-domains`.

### `Amplitude`

Loads Amplitude Browser SDK 2 asynchronously and initializes it after the script is available.

```astro
<Amplitude apiKey="YOUR_AMPLITUDE_API_KEY" />
<Amplitude apiKey="YOUR_AMPLITUDE_API_KEY" serverZone="EU" />
<Amplitude apiKey="YOUR_AMPLITUDE_API_KEY" src="https://cdn.amplitude.com/script/YOUR_AMPLITUDE_API_KEY.js" />
```

Props:
- `apiKey` (`string`, required): Amplitude project API key.
- `src` (`string`, optional): Override the SDK script URL for self-hosting or a custom CDN.
- `serverZone` (`'US' | 'EU'`, optional): Selects the default CDN and init options. Defaults to `US`.
- `autocapture` (`boolean`, optional): Passed to `window.amplitude.init()`. Defaults to `true`.
- `fetchRemoteConfig` (`boolean`, optional): Passed to `window.amplitude.init()`. Defaults to `true`.

### `Matomo`

Embeds the Matomo tracker using your Matomo base URL.

```astro
<Matomo id="1" url="https://analytics.example.com/" />
```

Props:
- `id` (`string`, required): Matomo site ID.
- `url` (`string`, required): Matomo base URL. The component derives `matomo.js` and `matomo.php` from this URL.

### `Clarity`

Embeds Microsoft Clarity using the standard bootstrap snippet.

```astro
<Clarity id="abcd1234xy" />
```

Props:
- `id` (`string`, required): Clarity project ID.

### `MinimalAnalytics` _(deprecated)_

Legacy Universal Analytics component kept for backward compatibility.

```astro
<MinimalAnalytics id="UA-XXXXXX-X" />
```

Props:
- `id` (`string`, required): Universal Analytics property ID.

Important notes:
- This component depends on the deprecated Universal Analytics `collect` endpoint.
- It is preserved for legacy migrations only.
- Do not use it for new Astro projects.
- Prefer `GoogleAnalytics`, `Plausible`, `Umami`, `Clarity`, `Fathom`, or another actively maintained provider instead.

## Performance guidance

These components are intentionally simple and keep runtime overhead low, but analytics always adds some network and script cost. For the best balance between accuracy and performance in Astro:

1. Render analytics in one shared layout only.
2. Enable analytics only in production.
3. Keep using async/defer-friendly providers and self-hosted `src` overrides when available.
4. Load analytics after consent if your privacy model requires it.
5. Avoid stacking multiple analytics vendors unless you truly need them.

### Recommended default

For most Astro sites, the best default is:
- inject once in a global layout,
- render only in production,
- keep the provider's normal async/defer loading behavior,
- avoid custom lazy-loading unless you have measured a real need.

### More aggressive optimization options

These can reduce impact further, but may trade away analytics completeness:
- Render only after a consent decision.
- Use provider-specific self-hosted `src` values to reduce third-party connection overhead and simplify CSP.
- Delay initialization until after the first screen is stable if you accept the risk of missing the first pageview or early events.

## Validation plan

Use this checklist after integration:

### 1. Verify rendered markup

Inspect the final HTML in your Astro app and confirm:
- `Plausible` renders `https://plausible.io/js/plausible.js` by default.
- `Umami` renders the expected `src` and `data-website-id` attributes.
- `Amplitude` loads the correct SDK URL for your `apiKey` and region.
- `Clarity` injects `https://www.clarity.ms/tag/<id>` through the bootstrap snippet.
- `Matomo` resolves `matomo.js` and `matomo.php` from your `url`.

### 2. Verify production-only behavior

Run your app locally and confirm analytics is not rendered when `import.meta.env.PROD` is false, if you gate it that way.

### 3. Verify network requests

Open browser DevTools and confirm the relevant analytics script and tracking requests are present:
- Plausible: `plausible.js`
- Umami: `umami.js`
- Amplitude: `cdn.amplitude.com` or `cdn.eu.amplitude.com`
- Clarity: `www.clarity.ms`
- Matomo: `matomo.js` and `matomo.php`

### 4. Verify dashboard ingestion

Open the provider dashboard and confirm pageviews or events arrive from a production-like environment.

### 5. Verify Astro client navigation scenarios

If your app uses `ClientRouter` or View Transitions, navigate between pages and confirm pageviews still register as expected. Some providers need SPA-specific configuration or manual pageview calls.

## Migration notes

- `Plausible` now defaults to `https://plausible.io/js/plausible.js`.
- `Umami` now supports the recommended `id` + `src` API while keeping `dataId` + `site` + `host` compatibility.
- `Amplitude` now uses an async Browser SDK 2 loading flow.
- `Clarity` now uses the standard bootstrap snippet instead of a partial script tag setup.
- `MinimalAnalytics` is now explicitly deprecated for new projects.
