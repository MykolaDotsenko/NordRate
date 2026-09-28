# NordRate — Nordic Currency Converter

[![Quality](https://github.com/MykolaDotsenko/NordRate/actions/workflows/quality.yml/badge.svg)](https://github.com/MykolaDotsenko/NordRate/actions/workflows/quality.yml)

**A dependency-free reference-rate converter built with native browser APIs.**

[**Open NordRate →**](https://mykoladotsenko.github.io/NordRate/) · [Architecture](./ARCHITECTURE.md)

![NordRate desktop interface](./docs/screenshots/nordrate-desktop.png)

NordRate is intentionally smaller than my Django-based Cultural Currency Converter. Its purpose is different: show a reliable API-driven interaction without a framework or backend.

## What it does

- edit either side of a conversion;
- swap currencies without losing the current value;
- expose the reference-rate date and reciprocal rate;
- accept decimal comma/point and common grouped-number formats;
- abort obsolete requests when the selected pair changes;
- show a recent saved rate when the provider cannot be refreshed;
- distinguish fresh, saved, loading, invalid and failure states.

The UI presents **reference rates**, not bank/card quotes.

## Reliability

### Old requests cannot overwrite a newer pair

Each new lookup aborts the previous request. A response is also checked against the currently selected pair before it can update state.

### Loading is bounded

Rate and currency-metadata calls have explicit timeouts instead of leaving the UI spinning indefinitely.

### Saved-rate fallback

Recent successful pair rates are stored in a versioned cache.

Malformed values, invalid dates, implausible timestamps and entries older than the accepted window are rejected. A cached value is visibly labelled **Saved rate** when a fresh request fails.

### Provider responses are checked

A rate reaches the UI only after the returned base, quote, date and positive numeric value match the expected contract.

## Architecture

```text
index.html + CSS
      ↓
   script.js
   /       \
rate API   rate cache
   \       /
    exchange.js
```

`exchange.js` owns parsing/conversion/formatting, `rate-client.js` owns the HTTP contract and `rate-cache.js` owns saved rates.

## Stack

- semantic HTML
- CSS
- Vanilla JavaScript / native ES modules
- Fetch + AbortController
- Web Storage
- Intl APIs
- Node test runner
- Playwright
- axe-core
- Lighthouse CI
- GitHub Actions

Zero runtime dependencies.

## Browser verification

The suite runs Chromium, Firefox, WebKit and mobile Chromium and covers conversion from both sides, swaps, quick pairs, invalid input, provider failures, saved fallback, stale-request protection and horizontal overflow.

Lighthouse gates performance/accessibility/best-practices/SEO rather than relying only on screenshots.

## Run locally

The product itself needs no install step:

```bash
python -m http.server 8000
```

For verification:

```bash
npm ci
npm run check
npx playwright install chromium firefox webkit
npm run test:e2e
npm run test:lighthouse
```

## Scope

There is deliberately no React layer, state library or backend. For one reference-rate screen, native modules keep the failure paths visible and testable.

## License

MIT © Mykola Dotsenko
