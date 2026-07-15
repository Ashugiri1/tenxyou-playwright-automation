TenXYou — Playwright Automation Suite

QA automation assignment for **https://tenxyou.com** (Sachin Tendulkar's sports brand).
Covers Login, Cart Management, Multi-Session behavior, Duplicate-Action handling, Cart
Persistence, and basic API-level validation — built with **Playwright + TypeScript**, Page
Object Model, and zero fixed (`waitForTimeout`) waits.

---

## 📁 Project Structure
tenxyou-playwright-automation/
├── pages/                  # Page Object Model
│   ├── BasePage.ts         # shared helpers: response-based waits, polling, logging
│   ├── LoginPage.ts        # phone + OTP login modal
│   ├── HomePage.ts         # header nav, search/wishlist/cart icons
│   ├── ProductPage.ts      # PDP: size/color select, Add to Kit
│   └── CartPage.ts         # "Your Kit" drawer: qty, remove, checkout
├── fixtures/
│   └── test-fixtures.ts    # injects page objects into test
├── utils/
│   └── test-data.ts        # env-driven, configurable test data (no hardcoding)
├── tests/
│   ├── 01-login.spec.ts
│   ├── 02-cart-management.spec.ts
│   ├── 03-multi-session.spec.ts
│   ├── 04-duplicate-actions.spec.ts
│   ├── 05-persistence.spec.ts
│   └── 06-api-validation.spec.ts
├── docs/
│   ├── TEST_CASES.md       # full test case document (core + edge cases)
│   ├── AI_USAGE.md         # required AI-usage disclosure
│   ├── SELECTOR_NOTES.md   # ⚠️ read this before running — selector caveats
│   └── EXECUTION_NOTES.md  # bugs/observations/assumptions template
├── playwright.config.ts
├── .env.example
└── package.json

---

## ⚠️ Read this first — one important caveat
This project was authored by inspecting the **screenshots you provided**, not a live,
JS-rendered browser session (the authoring sandbox can't execute arbitrary site JavaScript).
So:
1. Locators use Playwright's **resilient strategies** (`getByRole`, `getByPlaceholder`,
   `getByText`) matched to real, visible text from your screenshots — not fragile
   `nth-child`/positional CSS.
2. A few elements (logout control, remove-item icon, exact API paths) weren't visible in any
   screenshot, so they're best-effort with documented fallbacks. **Before your final
   submission, run:**
```bash
   npm install
   npm run codegen
```
   This opens Playwright Inspector against the live site — walk through login → add to cart →
   modify → checkout once, and reconcile any differing selectors in `pages/*.ts`.
   Full breakdown: **`docs/SELECTOR_NOTES.md`**.
3. **OTP login**: a production site sends a real SMS OTP that Playwright cannot read. This
   suite mocks the OTP-verify network call by default (`USE_MOCKED_OTP=true` in `.env`) so the
   full suite runs end-to-end deterministically. Set it to `false` and use `test:debug` /
   `--headed` if you want to key in a real OTP manually for one true end-to-end run.

---

## 🚀 Setup (run this in VS Code)

```bash
# 1. Install dependencies
npm install

# 2. Install Playwright's browser binaries
npx playwright install --with-deps chromium

# 3. Copy env file and adjust if needed
cp .env.example .env
```

## ▶️ Running tests

```bash
# Run everything, headless
npm test

# Run everything with a visible browser (recommended for your recording)
npm run test:headed

# Interactive UI mode - best for debugging step-by-step
npm run test:ui

# Run one scenario at a time
npm run test:login
npm run test:cart
npm run test:multisession
npm run test:duplicate
npm run test:persistence
npm run test:api

# View the last HTML report (traces, screenshots, videos on failure)
npm run report
```

## 🎥 Recording the mandatory execution video
Every run already saves a **video per test** on failure/retention (`video: 'retain-on-failure'`
in `playwright.config.ts`) plus full traces. For the assignment's required "complete flow,
nothing skipped" recording, the cleanest option is:
```bash
npm run test:headed
```
and screen-record the terminal + browser window, **or** temporarily set `video: 'on'` in
`playwright.config.ts` to force Playwright to save a video for every test regardless of pass/
fail, then pull the `.webm` files from `test-results/`.

---

## 🧱 Design decisions (mapped to assignment rules)

| Rule | How it's satisfied |
|---|---|
| No `waitForTimeout` | All waits are `page.waitForResponse()`, `expect().toBeVisible()`, or `expect.poll()` on real state (see `BasePage.ts`) |
| No fragile `nth-child` | Every locator uses role/text/placeholder-based strategies |
| Dynamic/configurable test data | `utils/test-data.ts` reads from `.env`, no inline hardcoded values in specs |
| POM | `pages/` holds all locators + actions; specs only orchestrate and assert |
| Debugging & observability | `BasePage.log()`, HTML+JSON reporters, trace/video/screenshot on failure |
| API awareness | `06-api-validation.spec.ts` intercepts and asserts on the raw request/response |
