# hcaptcha-puppeteer

Based on [aw1875/puppeteer-hcaptcha](https://github.com/aw1875/puppeteer-hcaptcha/), the deprecated package request-promise-native was replaced with Axios and now it's working properly.

## Install

```bash
npm i hcaptcha-puppeteer
```

## Usage

```javascript
await hcaptcha(page);
```

-   `page` [&lt;Page&gt;](https://pptr.dev/#?product=Puppeteer&version=v12.0.1&show=api-class-page) - Puppeteer Page Instance

```javascript
await hcaptchaToken(url);
```

-   `url` [&lt;string&gt;](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type) - URL of page with captcha on it

### Automatically set respone value ([see demo](https://github.com/aw1875/puppeteer-hcaptcha/blob/master/demos/solve.js))

```javascript
// Require puppeteer extra and puppeteer stealth
const puppeteer = require("puppeteer-extra");
const pluginStealth = require("puppeteer-extra-plugin-stealth");

// Require our hcaptcha method
const { hcaptcha } = require("hcaptcha-puppeteer");

// Tell puppeteer to use puppeteer stealth
puppeteer.use(pluginStealth());

(async () => {
    // Instantiate a new browser object
    // Ignore errors associated to https
    // Can be headless but for example sake we want to show the browser
    // Set your desired arguments for your puppeteer browser
    const browser = await puppeteer.launch({
        ignoreHTTPSErrors: true,
        headless: false,
        args: [
            `--window-size=600,1000`,
            "--window-position=000,000",
            "--disable-dev-shm-usage",
            "--no-sandbox",
            '--user-data-dir="/tmp/chromium"',
            "--disable-web-security",
            "--disable-features=site-per-process",
        ],
    });

    // Get browser pages
    const [page] = await browser.pages();

    // Send page to your url
    await page.goto("URL OF PAGE WITH CAPTCHA ON IT");

    // Remove the page's default timeout function
    await page.setDefaultNavigationTimeout(0);

    // Call hcaptcha method passing in our page
    await hcaptcha(page);

    // Your page is ready to submit.
    // Captcha solving should be the last function on your page so we
    // don't have to worry about the response token expiring.
    /**
     * Example:
     * await page.click("loginDiv > loginBtn");
     */
})();
```

### Return response token only ([see demo](https://github.com/aw1875/puppeteer-hcaptcha/blob/master/demos/token.js))

```javascript
// Require our hcaptchaToken method
const { hcaptchaToken } = require("puppeteer-hcaptcha");

(async () => {
    // Create Start Time
    const startTime = Date.now();

    // Call hcaptchaToken method passing in your url
    let token = await hcaptchaToken("URL OF PAGE WITH CAPTCHA ON IT");

    // Get End Time
    const endTime = Date.now();

    // Log timed result to console
    console.log(`Completed in ${(endTime - startTime) / 1000} seconds`);

    // P0_eyJ0eXAiOiJ...
    console.log(token);
})();
```