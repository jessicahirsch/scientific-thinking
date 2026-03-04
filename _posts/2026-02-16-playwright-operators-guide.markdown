---
layout: post
title:  "Playwright Operator's Guide"
date:   2025-02-16 11:18:00 -0700
categories: jekyll update
---

## Playwright 101
Playwright is a modern, cross-browser automation library for end-to-end testing. It can drive Chromium, Firefox, and WebKit and test anything that runs in a browser. Like Cypress, published tests are most effective when they are focused and avoid unnecessary dependencies.

### What does Playwright Offer?
Playwright ships with a test runner, trace viewer, screenshot and video capture, and a single API that works across browsers. Tests run in isolation by default. You get parallel execution out of the box and first-class support for multiple tabs, origins, and iframes.

### Playwright and Locators
Playwright encourages resilient locators. Prefer **role-based** and **text-based** selectors over raw CSS or XPath. The library auto-waits for elements to be actionable and retries assertions, which reduces flakiness. If you must use CSS or XPath, keep them short and maintain them in one place—see Page Object Model below.

### Playwright and Test Isolation
By default, each test gets a new browser context. That means a clean slate: no cookies, no storage, no leftover state. Isolation makes failures easier to reason about. When you need shared setup, use **fixtures** instead of sharing state between tests.

💡 **Protip** use `test.describe.configure({ mode: 'serial' })` only when tests in a file must run in order. Prefer parallel execution and independent tests.

### Playwright and iFrames
Playwright handles iframes without custom glue code. Use `frameLocator()` to scope into an iframe and then query elements inside it. No need to switch context manually as with some legacy tools.

## Fixtures
Playwright’s test runner is built on a **fixture** system. Fixtures provide your tests with everything they need: a `page`, a `context`, a `browser`, or your own custom objects (e.g. an authenticated page, a shared API client).

### Why Fixtures?
Fixtures give you dependency injection: declare what your test needs, and the runner creates and tears it down. They replace `beforeEach`/`afterEach` boilerplate and make it clear what each test depends on. Reusable fixtures keep your test files thin and consistent.

### Extending Built-in Fixtures
You extend the built-in `test` object by calling `test.extend()`. Add new fixture properties (e.g. `authenticatedPage`) that use the base `page` or `context`. Every test that requests `authenticatedPage` gets a page that’s already logged in or has the right cookies/storage—no repeated login logic in the test body.

### Base URL and Page Fixtures
Set `baseURL` in your config or in a fixture. Then use `page.goto('/path')` instead of full URLs. Page objects can assume a base URL and stay environment-agnostic.

## Page Object Model
As in the [Regression Test Operator's Guide]({{ site.baseurl }}/jekyll/update/2024/09/15/regression-test-operators-guide.html), the Page Object Model fits Playwright well. Each meaningful screen or flow has a **page class** (or page file) that encapsulates locators and actions.

### Page Files
Page files hold selectors and high-level actions (e.g. “log in”, “submit form”). Tests call methods on these classes instead of duplicating locators. When the UI changes, you update one page file instead of many tests.

### Locators in One Place
Centralize locators in page classes or in a shared constants/selectors module. Prefer Playwright’s `getByRole`, `getByText`, and `getByTestId` where possible. Keep complex or brittle selectors out of the test body so that maintenance stays predictable.

## API Auth
Running a full login flow in the UI for every test is slow and brittle. Playwright supports **authenticating via API** and reusing that state in browser tests.

### Request Context and Storage State
Use the **APIRequestContext** (from `request` fixture or `request.newContext()`) to sign in via HTTP: POST to your login endpoint, capture cookies or tokens from the response. Then call **`context.storageState()`** to serialize the current storage (cookies, local storage) and pass it when creating a new browser context so that tests start already logged in.

### One-Time Login, Many Tests
A common pattern: in a setup project or a before-all hook, perform API login once, save `storageState` to a file, and then in your test config use `storageState: 'auth.json'` so every test (or every test in that project) starts with an authenticated context. No UI login in the test. Fast and stable.

### Keeping Secrets Out of Tests
Put login credentials in environment variables or a `.env` file that is not committed. Use `process.env` in your fixture or config to pass them into the API login step. Never hardcode passwords in the repo.