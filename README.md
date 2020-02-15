# VueMatomo

[![npm](https://img.shields.io/npm/v/vue-matomo.svg)](https://www.npmjs.com/package/vue-matomo)
[![vue2](https://img.shields.io/badge/vue-2.x-brightgreen.svg)](https://vuejs.org/)
[![npm](https://img.shields.io/npm/dw/vue-matomo.svg)](https://www.npmjs.com/package/vue-matomo)

Link your Piwik/Matomo installation

## Installation

```bash
npm install --save vue-matomo
```

### Browser

```html
<!-- Include after Vue -->
<!-- Local files -->
<script src="vue-matomo/dist/vue-matomo.js"></script>

<!-- From CDN -->
<script src="https://unpkg.com/vue-matomo"></script>
```

## Usage

### Bundler (Webpack, Rollup)

```js
import Vue from 'vue'
import VueMatomo from 'vue-matomo'

Vue.use(VueMatomo, {
  // Configure your matomo server and site by providing
  host: 'https://matomo.example.com',
  siteId: 5,

  // Changes the default .js and .php endpoint's filename
  // Default: 'matomo'
  trackerFileName: 'matomo',

  // Overrides the autogenerated tracker endpoint entirely
  // Default: undefined
  // trackerUrl: 'https://example.com/whatever/endpoint/you/have',

  // Enables automatically registering pageviews on the router
  router: router,

  // Enables link tracking on regular links. Note that this won't
  // work for routing links (ie. internal Vue router links)
  // Default: true
  enableLinkTracking: true,

  // Require consent before sending tracking information to matomo
  // Default: false
  requireConsent: false,

  // Whether to track the initial page view
  // Default: true
  trackInitialView: true,

  // Run Matomo without cookies
  // Default: false
  disableCookies: false,

  // Enable the heartbeat timer (https://developer.matomo.org/guides/tracking-javascript-guide#accurately-measure-the-time-spent-on-each-page)
  // Default: false
  enableHeartBeatTimer: false,

  // Set the heartbeat timer interval
  // Default: 15
  heartBeatTimerInterval: 15,

  // Whether or not to log debug information
  // Default: false
  debug: false,

  // UserID passed to Matomo (see https://developer.matomo.org/guides/tracking-javascript-guide#user-id)
  // Default: undefined
  userId: undefined,

  // Share the tracking cookie across subdomains (see https://developer.matomo.org/guides/tracking-javascript-guide#measuring-domains-andor-sub-domains)
  cookieDomain: '*.example.com',

  // Tell Matomo the website domain so that clicks on these domains are not tracked as 'Outlinks'
  domains: '*.example.com'
});

// Now you can access piwik api in components through
this.$matomo

// or
window._paq.push

// or through
window.Piwik.getTracker
```

For available operations see the [matomo api docs](https://developer.matomo.org/api-reference/tracking-javascript)

#### Note on async loading

This plugin loads the `matomo.js` asynchronously, which means it is possible that `$matomo` is not (yet) loaded. Furthermore anti-tracking plugins on browsers might block `matomo.js` entirely. You should always guard your calls to `$matomo`, or use `window._paq.push`:

```js
this.$matomo && this.$matomo.trackPageView()

// Or...

window._paq.push(['trackPageView'])
```

### Nuxt

Nuxt can work by creating a plugin that will load VueMatomo with SSR disabled. Note how the router is passed in the second snippet:

```js
// nuxt.config.js

export default {
  plugins: [
    { src: '~/plugins/vue-matomo.js', ssr: false }
  ]
}

// plugins/vue-matomo.js

import Vue from 'vue'
import VueMatomo from 'vue-matomo'

export default ({ app }) => {
  Vue.use(VueMatomo, {
    router: app.router

    /** Other configuration options **/
  })
}
```

### Ignoring routes

It is possible to ignore routes using the route meta:

```js
{
  path: '/page-2',
  name: 'Page2',
  component: Page2,
  meta: {
    analyticsIgnore: true
  }
}
```

### Managing consent

First of all load the plugin with the `requireConsent` option enabled:

```js
Vue.use(VueMatomo, {
  // ...
  requireConsent: true
})
```

Matomo has a built in way to give and remember consent. The simplest way is to simply use this method provided by Matomo:

```js
<button @click="handleConsent()">Accept Cookies</button>

handleConsent() {
  this.$matomo.rememberConsentGiven()
}
```

Another option is to use your own implementation for remembering consent. In that case you can simply call
`this.$matomo.setConsentGiven()` on each page load when you establish that the user has given consent.

## Build

Bundle the js and css of to the `dist` folder:

```bash
npm run build
```

## License

[MIT](http://opensource.org/licenses/MIT)
