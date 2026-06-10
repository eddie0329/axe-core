# AGENTS.md

## Cursor Cloud specific instructions

### Product overview

**axe-core** is an accessibility testing engine (JavaScript library), not a standalone web app. Development centers on building `axe.js` / `axe.min.js` and running the test suites.

### Standard commands

See `CONTRIBUTING.md` and `doc/developer-guide.md` for full documentation.

| Task                    | Command                                             |
| ----------------------- | --------------------------------------------------- |
| Install deps            | `npm ci`                                            |
| Build                   | `npm run build` (required before tests)             |
| Lint                    | `npm run eslint`                                    |
| Format check            | `npm run fmt:check`                                 |
| All unit tests          | `npm test`                                          |
| Watch/rebuild on change | `npm run develop`                                   |
| Static test server      | `npm start` (port **9876**)                         |
| Node API smoke test     | `npm run test:node`                                 |
| Scoped unit tests       | `npm run test:unit:core`, `test:unit:commons`, etc. |

### Prerequisites

- **Node.js 18+** (enforced by `build/check-node-version.js`)
- **Chrome** (headless) for Karma unit tests — available as `google-chrome` in this environment
- **ChromeDriver** for WebDriver integration tests — install with `npx browser-driver-manager install chromedriver` if integration tests fail with driver errors

### Gotchas

1. **Build before tests**: `axe.js` must exist (`npm run build`) before running `npm test` or Karma suites.
2. **No docker-compose**: CI uses CircleCI (`cimg/node:18.18-browsers`) and GitHub Actions; local setup is `npm ci` + build + Chrome.
3. **Integration tests** (`npm run test:integration`) start an HTTP server on port 9876 via `start-server-and-test` and need ChromeDriver.
4. **Pre-commit hook** (`.husky/pre-commit`) runs `grunt configure` and `lint-staged`; commits trigger formatting/ESLint fixes.
5. **`npm run test:node`** installs a compatible `jsdom` version into `test/node/` on each run (not saved to lockfile).

### Hello-world verification

After `npm ci && npm run build`, run:

```bash
npm run test:node
```

Or scan sample HTML with axe + jsdom (see `test/node/jsdom.js` for the correct API: pass `document.documentElement`, not bare `document`).
