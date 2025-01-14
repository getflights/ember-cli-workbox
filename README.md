# ember-cli-workbox

[![Build Status](https://travis-ci.org/BBVAEngineering/ember-cli-workbox.svg?branch=master)](https://travis-ci.org/BBVAEngineering/ember-cli-workbox)
[![GitHub version](https://badge.fury.io/gh/BBVAEngineering%2Fember-cli-workbox.svg)](https://badge.fury.io/gh/BBVAEngineering%2Fember-cli-workbox)
[![NPM version](https://badge.fury.io/js/ember-cli-workbox.svg)](https://badge.fury.io/js/ember-cli-workbox)
[![Dependency Status](https://david-dm.org/BBVAEngineering/ember-cli-workbox.svg)](https://david-dm.org/BBVAEngineering/ember-cli-workbox)
[![codecov](https://codecov.io/gh/BBVAEngineering/ember-cli-workbox/branch/master/graph/badge.svg)](https://codecov.io/gh/BBVAEngineering/ember-cli-workbox)
[![Greenkeeper badge](https://badges.greenkeeper.io/BBVAEngineering/ember-cli-workbox.svg)](https://greenkeeper.io/)
[![Ember Observer Score](https://emberobserver.com/badges/ember-cli-workbox.svg)](https://emberobserver.com/addons/ember-cli-workbox)

## Information

[![NPM](https://nodei.co/npm/ember-cli-workbox.png?downloads=true&downloadRank=true)](https://nodei.co/npm/ember-cli-workbox/)

A plugin for your Ember-cli build process, giving your app offline caching as a progressive enhancement, using service workers. Ember-cli-workbox will add a service worker to your Ember App registering it on initial page load.

This addon simplify service worker registration and caching, powered by [workbox-build](https://www.npmjs.com/package/workbox-build). Workbox library automate precaching of static resources (HTML, JavaScript, CSS, and images) and handle runtime caching and fallback strategies. It allowed us to implement a performant strategy in which a static content is always served directly from the cache, and dynamic or remote resources are served from the network, with fallbacks to cached or static responses when needed.

For more details on Workbox check out:

- [Workbox Google Developers](https://developers.google.com/web/tools/workbox/)
- [Service Workers cookbook](https://serviceworke.rs/)

## Installation

`ember install ember-cli-workbox`

## Dependencies

"ember-cli-terser": "^2.0.0",
(previously ember-cli-uglify)

## Configuration

If you need to customize **ember-cli-workbox configuration** you can do it like this:

```javascript
// app/config/environment.js

ENV['ember-cli-workbox'] = {
  enabled: environment !== 'test',
  debug: true,
  autoRegister: true,
  importScriptsGlobPatterns: ['assets/service-workers/*.js'],
};
```

```javascript
// ember-cli-build.js
const app = new EmberAddon(defaults, {
  'ember-cli-workbox': {
    importScriptsTransform(importScripts) {
      return importScripts.map(
        (importScript) => `https://example-cdn.com/${importScript}`
      );
    },
  },
});
```

|          Property           |    Type    |                                                                            Description                                                                             |
| :-------------------------: | :--------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|          `enabled`          | `Boolean`  |                                                      Addon is enabled. Defaults `true` for production builds                                                       |
|           `debug`           | `Boolean`  |                                                       Log serviceworker states (registering, updating, etc)                                                        |
|       `autoRegister`        | `Boolean`  |                                                   Enable the sw registration before initializing the application                                                   |
|  `importScriptsTransform`   | `Function` | Allows for transformation of array sent to workbox [importScripts](https://developers.google.com/web/tools/workbox/modules/workbox-build#generateSW-importScripts) |
| `importScriptsGlobPatterns` |  `Array`   |  Define files that are going to be imported using [importScripts](https://developers.google.com/web/tools/workbox/modules/workbox-build#generateSW-importScripts)  |

You can further customize ember-cli-workbox by setting **workbox configurations** in your `config/environment.js` on in your `ember-cli-build.js`

_Note_: `importScriptsTransform` and `workbox` must be defined in your `ember-cli-build.js`.

```javascript
//ember-cli-build.js

const app = new EmberAddon(defaults, {
  workbox: {
    globPatterns: ['**/*.{html,js,css}'],

    globDirectory: './',

    globIgnores: [],
    // ...
  },
});
```

|            Property             |                                                                                                                                                                                                                       Description                                                                                                                                                                                                                       |
| :-----------------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|            `swDest`             |                                                                                                                                                      The path to the final service worker file that will be created by the build process, relative to the build directory. Default path: `./sw.js`                                                                                                                                                      |
|         `globPatterns`          |                                                                                                           Files matching against any of these glob patterns will be included in the precache manifest. By default sw precaches all our ember application assets that match `**/*.{json,css,js,png,svg,eot,ttf,woff,jpg,gif,ico,xml,html,txt}`                                                                                                           |
|         `globDirectory`         |                                                                                                                                                                         The base directory you wish to match globPatterns against, related to the build directory. Default './'                                                                                                                                                                         |
|          `globIgnores`          |                                                                                                                                     Files matching against any of these glob patterns will be excluded from the file manifest, overriding any matches from globPatterns (E.g. `globIgnores: ['**\/ignored.html']`)                                                                                                                                      |
|         `templatedUrls`         |                                                                                                                                                     If a URL is rendered generated based on some server-side logic, its contents may depend on multiple files or on some other unique string value.                                                                                                                                                     |
|            `cacheId`            |                                                                                                           An optional ID to be prepended to caches used by workbox-sw. This is primarily useful for local development where multiple sites may be served from the same http://localhost origin. Defaults to your app name (config.APP.name).                                                                                                            |
| `maximumFileSizeToCacheInBytes` |                                                                                                                                                                                  This value can be used to determine the maximum size of files that will be precached                                                                                                                                                                                   |
|        `runtimeCaching`         | Passing in an array of objects containing urlPatterns, handlers, and potentially options that will add the appropriate code to the generated service worker to handle runtime caching. The handler values correspond the names of the [strategies](https://developers.google.com/web/tools/workbox/reference-docs/latest/module-workbox-sw.Strategies) supported by workbox-sw (cacheFirst, cacheOnly, networkFirst, networkOnly, staleWhileRevalidate) |

```javascript
runtimeCaching: [
  {
    // You can use a RegExp as the pattern:
    urlPattern: /https://api.example.com/,
    handler: 'cacheFirst',
    // Any options provided will be used when
    // creating the caching strategy.
    options: {
      cacheName: 'my-api-cache',
      cacheExpiration: {
        maxEntries: 10,
      },
    },
  },
  // ...
]
```

Note that `importScripts` parameter is overriden by this addon to include all js files on `/public/assets/service-workers/*` folder. If you want to change this path use `importScriptsGlobPatterns` option.

> For more details on Workbox configuration take a look at: [Workbox Google Developers](https://developers.google.com/web/tools/workbox/reference-docs/latest/module-workbox-build).

## Ember service

This addon bundles a new Ember service called `service-worker`.
This service will register/unregister the workers when necessary.

|   Property    |   Type    |           Description            |
| :-----------: | :-------: | :------------------------------: |
|     `sw`      | `Object`  |       The navigator SW API       |
| `isSupported` | `Boolean` | Navigator is complatible with SW |

**Methods:**

- `register(swFile)`: Registers new service worker given a file path.
- `unregisterAll()`: Unregisters all service workers.

## Subscribing to events

If you are using workbox with `clientsClaim: true` and `skipWaiting: true` your serviceWorker will became active automaticatly.
In this case, if you want to force reload simply do this:

```JavaScript
// "this.serviceWorker" means the Ember service
this.serviceWorker.on('activated', () => {
  console.log('New service worker controlling the page. Forcing reload to apply new changes.');
  window.location.reload();
});
```

But if you want to take control of what is the state of serviceWorker, do not activate `clientsClaim` and `skipWaiting`.
The recomendation is using the Ember's `service-worker` service (bundled with this addon) that triggers the following events:

- `error`: SW not registered.
- `waiting`: New SW waiting for controlling page.
- `activated`: The new SW is ready to respond.
- `registrationComplete`: SW successfully registered.
- `unregistrationComplete`: All SW are unregistered.

### Why and how to use this events?

By default, users have to close all tabs to a site in order to update a Service Worker (the Refresh button is not enough).
If you make a mistake here, **users will see an outdated version of your site** even after refreshing.
Service Workers break the Refresh button because they behave like “apps,” refusing to update while the app is still running, in order to maintain code consistency and client-side data consistency. We can write code to notify users when a new version is available.

This addon make it easy for you and implements [google recommendation](https://developers.google.com/web/tools/workbox/guides/advanced-recipes#offer_a_page_reload_for_users).
Basically, what you have to do is subscribing to the event `waiting`. When event is triggered, send a message to sw in order to launch `skipWaiting + clients.claim` on it to turn it active (you can do this just calling forceActivate method on serviceWorkerService). When service worker became active it will send a message "reload-window" and "newSWActive" will be triggered.

**Example:**

```javascript
// <my-app>/mixins/service-worker-states.js

import Route from '@ember/routing/route';
import { inject as service } from '@ember/service';

export default class MyRoute extends Route {
  @service serviceWorker;

  async beforeModel() {
    await super.beforeModel(...arguments);

    // Do not call this event twice!
    this.subscribeToSWEvents();
  }

  subscribeToSWEvents() {
    this.serviceWorker.on('activated', (reg) => {
      window.alert('Content is now available offline!');
    });

    this.serviceWorker.on('waiting', (reg) => {
      if (window.confirm('New version available! Refresh?')) {
        sw.forceActivate(reg);
      }
    });

    this.serviceWorker.on('updated', () => {
      window.location.reload();
    });
  }
}
```

## Prevent caching lazy engines

By default, this addons precaches everything, that means all the lazy engines will be precached in your service worker.
To prevent precaching it, just exclude the `engine-dist` in the addon config:

```javascript
var ENV = {
  workbox: {
    globIgnores: ['engines-dist/**/*'],

    runtimeCaching: [
      {
        urlPattern: /engines-dist/,
        handler: 'networkFirst',
      },
    ],
  },
};
```

## Debugging serviceWorker generation on build

```bash
$ DEBUG=ember-cli:workbox ember s
```

## Future improvements

`ember-cli-workbox` currently do not implement `workboxBuild.injectManifest()` feature, only works generating a new serviceworker.

**What is injectManifest feature?**

If you have an existing serviceWorker, `workbox-build` can modify it to inject the manifest file to precache our static files.
Basically you should have a placeholder array which is populated automatically by `workboxBuild.injectManifest()`.

```javascript
// my existing service worker
workboxSW.precache([]);
```

Sometimes you'll need more control over what is cached, strategies used and custom code inside the server worker. You can do this by setting your own service worker and using the `WorkboxSW` object directly.

## Contributing

We're thankful to the community for contributing any improvements.

Do not forget to follow our `eslint` rules and make test for the new functionalities/fixes.
