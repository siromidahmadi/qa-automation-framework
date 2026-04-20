# qa-automation-framework


qa-automation-framework/
├── README.md
├── package.json
├── playwright.config.js
├── .github/
│   └── workflows/
│       └── tests.yml
├── pages/
│   └── loginPage.js
└── tests/
    └── login.spec.js
README.md
# QA Automation Framework

![Tests](https://img.shields.io/badge/tests-passing-brightgreen)
![Framework](https://img.shields.io/badge/framework-Playwright-green)
![Language](https://img.shields.io/badge/code-JavaScript-yellow)

A scalable UI automation framework built with Playwright using reusable page objects and clean test structure.

## Features
- Page Object Model
- Reusable test design
- Easy configuration
- CI with GitHub Actions

## Run
```bash
npm install
npx playwright install
npx playwright test

### `package.json`
```json
{
  "name": "qa-automation-framework",
  "version": "1.0.0",
  "scripts": {
    "test": "playwright test"
  },
  "devDependencies": {
    "@playwright/test": "^1.54.0"
  }
}
playwright.config.js
const { defineConfig } = require('@playwright/test');

module.exports = defineConfig({
  testDir: './tests',
  use: {
    headless: true,
    baseURL: 'https://the-internet.herokuapp.com'
  }
});
pages/loginPage.js
class LoginPage {
  constructor(page) {
    this.page = page;
    this.username = '#username';
    this.password = '#password';
    this.loginButton = 'button[type="submit"]';
    this.flashMessage = '#flash';
  }

  async goto() {
    await this.page.goto('/login');
  }

  async login(user, pass) {
    await this.page.fill(this.username, user);
    await this.page.fill(this.password, pass);
    await this.page.click(this.loginButton);
  }

  async getMessage() {
    return await this.page.textContent(this.flashMessage);
  }
}

module.exports = { LoginPage };
tests/login.spec.js
const { test, expect } = require('@playwright/test');
const { LoginPage } = require('../pages/loginPage');

test('valid login', async ({ page }) => {
  const loginPage = new LoginPage(page);

  await loginPage.goto();
  await loginPage.login('tomsmith', 'SuperSecretPassword!');
  const message = await loginPage.getMessage();

  await expect(message).toContain('You logged into a secure area!');
});
.github/workflows/tests.yml
name: Playwright Tests

on:
  push:
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm install

      - name: Install Playwright browsers
        run: npx playwright install --with-deps

      - name: Run tests
        run: npx playwright test
