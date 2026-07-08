# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Static, single-page marketing/catalog site for "Dulce Encanto" (artisan alfajores business, Chile). Plain HTML/CSS/vanilla JS — no build step, no package manager, no framework.

- `index.html` — full page markup: header/nav, hero, catalog section, about, contact, and 3 modals (product detail, admin login, admin CRUD panel).
- `app.js` — all application logic (vanilla JS, no modules/bundler).
- `style.css` — all styling.
- `assets/` — product images.

## Running / testing

No build/test/lint tooling in this repo. To work on it, just open `index.html` directly in a browser or serve the directory statically (e.g. `npx serve .`). Changes to `app.js`/`style.css`/`index.html` are reflected on reload — no compilation step.

## Architecture

**Data & state**: Product catalog lives in `DEFAULT_PRODUCTS` (array in `app.js`) and is persisted to `localStorage` under key `dulce_encanto_products`. On `init()`, data loads from localStorage if present, otherwise seeds from `DEFAULT_PRODUCTS` and writes it. All CRUD (admin add/edit/delete/reset) mutates the in-memory `products` array then calls `saveProductsToStorage()`. There is no backend — everything is client-side and per-browser.

**Rendering**: No templating engine. `renderCatalog()` and `renderAdminTable()` rebuild DOM via `innerHTML` string templates on every state change (filter, search, CRUD op). Product cards wire their own click handlers after insertion.

**"Admin" panel**: Client-side-only gate — password (`ADMIN_PASSWORD` constant in `app.js`, currently `"admin2026"`) is checked in JS and shipped in cleartext to every visitor. This is not real auth; treat it as a UI convenience only, not a security boundary. The admin panel lets you add/edit/delete products (including uploading images as base64 data URLs stored directly in localStorage) and reset to `DEFAULT_PRODUCTS`.

**Ordering flow**: There's no cart/checkout backend. "Ordering" a product or submitting the contact form builds a formatted message and opens `https://api.whatsapp.com/send?phone=...&text=...` (see `WHATSAPP_PHONE` constant) in a new tab — orders/inquiries are handled manually via WhatsApp, not processed by this app.

**Modals**: Three modals (`#modal-product-detail`, `#modal-admin-login`, `#modal-admin-panel`) toggle via `openModal`/`closeModal` helpers (`.active` class + `aria-hidden`), not `<dialog>`.

**IDs as the wiring mechanism**: `app.js` queries elements almost entirely by `id` (declared as `const` at top of file) rather than event delegation or component structure — when editing `index.html` markup, preserve existing `id`s or update every corresponding reference in `app.js`.
