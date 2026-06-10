# AGENTS.md

## Cursor Cloud specific instructions

### Product overview

**axe-core** is a client-side JavaScript accessibility testing engine (npm library). It is not a hosted web app. The main deliverables are `axe.js` / `axe.min.js`, built from `lib/` via Grunt.

### Prerequisites

- **Node.js ≥ 18** (enforced by `build/check-node-version.js`)
- **Google Chrome** + **ChromeDriver** (compatible versions) for Karma and Selenium integration tests
- No `.env` file or external services (database, Docker, etc.) are required

### Dependency install & build

```bash
npm ci
npm run build          # required — axe.js is gitignored and must be built before tests
npx browser-driver-manager install chromedriver   # align ChromeDriver with installed Chrome
```

### Common commands

| Task                                    | Command                                                      |
| --------------------------------------- | ------------------------------------------------------------ |
| Build                                   | `npm run build`                                              |
| Lint (ESLint)                           | `npm run eslint`                                             |
| Format check                            | `npm run fmt:check`                                          |
| TypeScript defs                         | `npm run test:tsc`                                           |
| All unit tests (Karma + ChromeHeadless) | `npm test`                                                   |
| Scoped unit tests                       | `npm run test:unit:core`, `test:unit:api`, etc.              |
| Node/JSDOM tests (no browser)           | `npm run test:jsdom`, `npm run test:node`                    |
| Full-page Selenium E2E                  | `npm run test:integration` (starts http-server on port 9876) |
| Dev watch mode                          | `npm run develop`                                            |

See `CONTRIBUTING.md` and `doc/developer-guide.md` for full testing documentation.

### Gotchas

1. **Always build before testing.** `axe.js` is listed in `.gitignore`; a fresh clone or `npm ci` does not produce it.
2. **ChromeDriver version must match Chrome.** Run `npx browser-driver-manager install chromedriver` after Chrome updates or on a new VM.
3. **Integration tests need port 9876.** `npm run test:integration` uses `start-server-and-test` to serve fixtures via `http-server`. Ensure nothing else binds to 9876.
4. **Full `npm test` is slow** (thousands of Karma tests). For quick validation, use `npm run test:unit:api` or `npm run test:jsdom`.
5. **Pre-commit hook** (`.husky/pre-commit`) runs `npx grunt configure` and `npx lint-staged` (Prettier + ESLint on staged files).
6. **`color-contrast` rule does not work in JSDOM** — disable it in Node/JSDOM demos: `{ rules: { 'color-contrast': { enabled: false } } }`.
7. **One integration test may fail in headless Chrome** (`test/integration/full/preload/preload.html` — stylesheet preload). This is a known flaky/environment-specific case; the rest of the integration suite passes.

### Hello-world verification

After `npm ci && npm run build`, confirm the engine works:

```bash
node -e "
const axe = require('./');
const { JSDOM } = require('jsdom');
const dom = new JSDOM('<!DOCTYPE html><html><body><img src=\"x.png\"></body></html>');
axe.run(dom.window.document.documentElement, { rules: { 'color-contrast': { enabled: false } } })
  .then(r => console.log('violations:', r.violations.map(v => v.id).join(', ')));
"
```

Expected output includes violations such as `html-has-lang`, `image-alt`, `document-title`.
