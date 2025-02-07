<p align="center">
  <a href="https://sentry.io/?utm_source=github&utm_medium=logo" target="_blank">
    <img src="https://sentry-brand.storage.googleapis.com/sentry-wordmark-dark-280x84.png" alt="Sentry" width="280" height="84">
  </a>
</p>

# Official Sentry SDK for Solid

[![npm version](https://img.shields.io/npm/v/@sentry/solid.svg)](https://www.npmjs.com/package/@sentry/solid)
[![npm dm](https://img.shields.io/npm/dm/@sentry/solid.svg)](https://www.npmjs.com/package/@sentry/solid)
[![npm dt](https://img.shields.io/npm/dt/@sentry/solid.svg)](https://www.npmjs.com/package/@sentry/solid)

This SDK is considered **experimental and in an alpha state**. It may experience breaking changes. Please reach out on
[GitHub](https://github.com/getsentry/sentry-javascript/issues/new/choose) if you have any feedback or concerns. This
SDK currently only supports [Solid](https://www.solidjs.com/) and is not yet officially compatible with
[Solid Start](https://start.solidjs.com/).

# Solid Router

The Solid Router instrumentation uses the Solid Router library to create navigation spans to ensure you collect
meaningful performance data about the health of your page loads and associated requests.

Add `Sentry.solidRouterBrowserTracingIntegration` instead of the regular `Sentry.browserTracingIntegration` and provide
the hooks it needs to enable performance tracing:

`useBeforeLeave` from `@solidjs/router`  
`useLocation` from `@solidjs/router`

Make sure `Sentry.solidRouterBrowserTracingIntegration` is initialized by your `Sentry.init` call, before you wrap
`Router`. Otherwise, the routing instrumentation may not work properly.

Wrap `Router`, `MemoryRouter` or `HashRouter` from `@solidjs/router` using `Sentry.withSentryRouterRouting`. This
creates a higher order component, which will enable Sentry to reach your router context.

```js
import * as Sentry from '@sentry/solid';
import { Route, Router, useBeforeLeave, useLocation } from '@solidjs/router';

Sentry.init({
  dsn: '__PUBLIC_DSN__',
  integrations: [Sentry.solidRouterBrowserTracingIntegration({ useBeforeLeave, useLocation })],
  tracesSampleRate: 1.0, //  Capture 100% of the transactions
});

const SentryRouter = Sentry.withSentryRouterRouting(Router);

render(
  () => (
    <SentryRouter>
      <Route path="/" component={App} />
      ...
    </SentryRouter>
  ),
  document.getElementById('root'),
);
```

# ErrorBoundary

To automatically capture exceptions from inside a component tree and render a fallback component, wrap the native Solid
JS `ErrorBoundary` component with `Sentry.withSentryErrorBoundary`.

```js
import * as Sentry from '@sentry/solid';
import { ErrorBoundary } from 'solid-js';

Sentry.init({
  dsn: '__PUBLIC_DSN__',
  tracesSampleRate: 1.0, //  Capture 100% of the transactions
});

const SentryErrorBoundary = Sentry.withSentryErrorBoundary(ErrorBoundary);

render(
  () => (
    <SentryErrorBoundary fallback={err => <div>Error: {err.message}</div>}>
      <ProblematicComponent />
    </SentryErrorBoundary>
  ),
  document.getElementById('root'),
);
```

# Sourcemaps and Releases

To generate and upload source maps of your Solid JS app bundle, check our guide
[how to configure your bundler](https://docs.sentry.io/platforms/javascript/guides/solid/sourcemaps/#uploading-source-maps)
to emit source maps.
