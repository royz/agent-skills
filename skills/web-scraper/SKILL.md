---
name: web-scraper
description: Implement public, single-page Node.js web scrapers with native fetch and Linkedom, including inspecting live pages and contrasting states, installing Linkedom in the correct project package, and returning requested visible HTML data as a JavaScript object. Use when the user supplies a public URL and asks to extract product price or availability, article metadata, listing details, or other server-rendered page data, or asks to write or repair fetch-and-parse scraper code. Do not use for authenticated pages, general crawling, browser automation, or content available only after client-side JavaScript executes.
---

# Web Scraper

Implement a live-page-informed Node.js scraper with native `fetch` and Linkedom. Keep network retrieval separate from pure HTML parsing.

## Establish the contract

1. Identify the target URL, requested fields, required output keys, and destination project from the user's request.
2. Infer clear field names and return types without asking unnecessary questions. If formatting is unspecified, return trimmed user-visible strings and preserve currency symbols, units, and wording.
3. Default to one public page. Add pagination, link discovery, authentication, or browser-backed execution only when the user explicitly changes the scope.
4. Export exactly two functions unless the user requests another interface:
   - A URL-to-HTML function returning `Promise<string>`.
   - A pure HTML-to-object parser with no network access.
5. Allow private helpers, but do not export a third convenience wrapper by default.

## Inspect the project and install Linkedom

1. Read repository instructions and inspect the existing scraper location, language, module system, naming, error handling, and package-manager files.
2. Match the leaf project's JavaScript or TypeScript and ESM or CommonJS conventions.
3. Check whether `linkedom` is already a direct dependency of the leaf package that will import it.
4. If it is absent, add it with the project's package manager. In a monorepo, target the leaf workspace from that workspace or with the package manager's workspace filter; never add it to the workspace root merely because the lockfile is there.
5. Let the package manager update the manifest and lockfile. Do not hand-edit dependency entries or lockfiles.

## Investigate the live page

1. Open the target in a real browser-capable tool and inspect both its visible layout and DOM.
2. Confirm the exact entity represented by the page and locate the smallest primary container that owns the requested values.
3. When the target state omits a requested field, inspect enough same-host, same-locale, same-template pages to cover the contrasting visible states, normally one or two pages. For example, compare unavailable and available product pages to learn both offer layouts.
4. Use comparison pages only to understand the shared template. Do not substitute their values for the target's values.
5. Prefer stable selectors scoped to the primary container: semantic IDs, data attributes, item attributes, or durable class relationships. Avoid broad document-wide price selectors, brittle positional selectors, and recommendation or advertising regions.
6. Extract only visible DOM content. Do not use JSON-LD, hydration payloads, serialized application state, or other embedded script data as a fallback.
7. Verify that every chosen node exists in the raw HTML returned by the intended fetch request. Browser-rendered DOM alone is not evidence that a fetch-plus-Linkedom scraper can parse the value.

## Implement HTML retrieval

1. Use native `fetch`; do not add another HTTP client.
2. Obtain the current user-agent from the browser used for inspection when possible. Otherwise verify a current stable Chrome-like user-agent at task time. Do not copy a stale user-agent from this skill.
3. Always send that `User-Agent`, an HTML navigation `Accept` header, and an `Accept-Language` appropriate for the inspected page locale.
4. Validate the response status and reject non-HTML, login, CAPTCHA, bot-challenge, consent-interstitial, or otherwise unusable responses with a descriptive error.
5. If the first public request is challenged, make at most one targeted retry with coherent, non-sensitive browser navigation headers justified by the inspected request. Keep client hints consistent; do not invent them.
6. Never copy browser cookies, credentials, authorization tokens, or private session headers. Never solve CAPTCHAs, rotate proxies, or silently switch to browser automation.
7. Return only the usable HTML string. If public raw HTML remains blocked or contains only a JavaScript shell, stop and explain that this scraper architecture is not viable for the page.

## Implement pure parsing

1. Parse the supplied HTML with Linkedom's `DOMParser` and `text/html`.
2. Validate the page or entity identity before extracting requested values. Throw when the HTML is a different page, an error page, or an unexpected template.
3. Query within the verified primary container and return exactly the requested JavaScript object shape.
4. Normalize layout whitespace only. Do not silently convert displayed prices, dates, units, or statuses unless the user requested normalized types.
5. Return `null` only when a verified visible domain state legitimately omits a value. Throw a descriptive parse error when a required node is unexpectedly absent or ambiguous.
6. Do not infer availability from price presence or absence alone. Require explicit visible availability or purchase-state signals tied to the exact item's primary offer.
7. On marketplace pages, exclude sponsored items, recommendations, accessories, warranties, alternate products, and unrelated seller prices unless the user explicitly requests them.

## Validate live behavior

1. Run the actual exported fetch and parser functions against the target URL.
2. Run them against every contrasting page used to derive state-specific selectors.
3. Compare each returned value with the visible value in the same page's verified primary container. Independently verify every `null` result against an explicit legitimate state.
4. Run existing project typecheck, lint, or test commands that cover the changed leaf package. Do not introduce fixtures or a new test framework; keep scraper verification live-page based unless the user requests tests.
5. Confirm that only two functions are exported and that the parser performs no network access.
6. Report inaccessible pages, intermittent blocking, or raw-versus-rendered HTML differences as validation limits. Do not claim success when the live fetch path cannot reproduce the inspected data.

## Review common scenarios

- For an unavailable Amazon product, validate the exact product and its primary offer, return a null price only alongside explicit unavailable text, and confirm the price selector on a comparable available listing.
- For an article or detail page, scope author, date, title, or other requested fields to the main record rather than navigation, related content, or cards.
- For a monorepo, place scraper code and the direct `linkedom` dependency in the owning leaf package while preserving root workspace configuration.
