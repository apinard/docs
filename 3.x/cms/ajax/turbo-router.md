---
subtitle: Learn how links are routed using the AJAX framework.
---
# Turbo Router

Turbo routing is an implementation of PJAX (push state and AJAX) that gives the performance benefits of a single page application without the added complexity of a client-side framework. When you click a link, the page is automatically swapped client-side without the cost of a full page load.

```twig
{% framework turbo %}
```

## Routing Links

You may programmatically visit a link with the following.

```js
oc.visit(location);
```

To replace the current URL without adding it to the navigation history, similar to `window.history.replaceState`, set the `action` option to **replace**.

```js
oc.visit(location, { action: 'replace' });
```

To check if the turbo router is enabled and should be used.

```js
if (oc.useTurbo && oc.useTurbo()) {
    // Use PJAX
}
```

## Disable Routing

To disable PJAX routing on a specific page, you may trigger a full reload by including the `turbo-visit-control` meta tag in the head section of the page with the `reload` value. This will disable the feature for incoming requests only.

```html
<head>
    <meta name="turbo-visit-control" content="reload" />
</head>
```

To completely disable PJAX in your website, set the value to `disable`. This will disable the feature for all incoming and outgoing requests.

```html
<meta name="turbo-visit-control" content="disable" />
```

## Disable for Specific Links

By default, all internal HTML links will be routed using PJAX, but you can disable this by marking links or their parent container with `data-turbo="false"`. Links that are disabled are handled normally by the browser.

```html
<a href="/" data-turbo="false">Disabled</a>
```

You may re-enable when an ancestor has disabled:

```html
<div data-turbo="false">
    <a href="/" data-turbo="true">Enabled</a>
</div>
```

## Disable Visit Scrolling

Every visit scrolls to the top of the page like most links in a browser. However, in some cases preserving the scroll position is useful, such as situations where links act like filters. You may disable visit scrolling using the `data-turbo-no-scroll` attribute on the link element.

```html
<a href="/" data-turbo-no-scroll>Filter</a>
```

## Persisting Page Elements

In some cases you may want to include static elements on the page, these are elements that should not be refreshed when the page updates. Use the `data-turbo-permanent` attribute on the parent element. The element must also supply and `id` attribute so the original page can be matched with the new page, including event listeners.

```html
<div id="main-navigation" data-turbo-permanent>...</div>
```

## Setting a Root Path

By default, PJAX is used for all links within the same domain name and a visit to any other URL will fallback to a full page load. In some cases, your application may live in a subdirectory and the links should only apply to the root path.

For example, if you website lives in `/app` and you don't want the links to apply to a different site in `/docs` then you may restrict the links to a root path. You may set the root path by including the `turbo-root` meta tag in the page's head section.

```html
<head>
    <meta name="turbo-root" content="/app">
</head>
```

## Native Error Pages

When using PJAX and the server responds with an error code, such as 404 or 500 status, the complete html element is replaced, including scripts and stylesheets. This prevents accidentally replacing the body element with content not produced by the same application code.

You may disable this behavior by including the `turbo-visit-control` meta tag in the head section of the page with the `error` value. This will tell the turbo router that the error page content is produced by the native application.

```html
<meta name="turbo-visit-control" content="error">
```

## Page Caching

With caching enabled, the turbo router speeds up a website's performance by displaying revisited pages without accessing the network, making the website feel faster. When clicking a link, the contents are shown from the browser's local storage while the page requests the background. The latest page shows when the network request is complete, meaning the page renders twice.

### Listening for the Cache Event

You may listen to the `page:before-cache` event if you need to prepare the document before it enters the cache. You can use this for things like resetting forms, collapsing UI elements or tearing down any third-party controls, so the page is ready to be displayed again.

```js
addEventListener('page:before-cache', function() {
    // Close any open submenus, etc.
});
```

### Detecting a Cached Page Load

You can detect when the page contents are sourced from the cache with the `data-turbo-preview` attribute on the HTML element. Expressed in JavaScript as the following.

```js
if (document.documentElement.hasAttribute('data-turbo-preview')) {
    // Page shown is loaded from cache
}
```

Or using a StyleSheet with the following.

```css
html[data-turbo-preview] {
    /* Hide overlays from previous view */
}
```

### Disabling the Cache

You can disable the page cache for individual pages by using the `turbo-cache-control` meta tag in the page's head section. Settings this value to **no-cache** will disable the cache entirely. You can also set this to **no-preview** to keep the cached version when navigating using the browser's Back and Forward buttons.

```html
<head>
    <meta name="turbo-cache-control" content="no-cache">
</head>
```

## Working with JavaScript

When working with PJAX, the page contents may load dynamically, which differs from the usual browser behavior. To overcome this, use the `render` event handler is called every time the page loads, including [AJAX updates](./update-partials.md).

```js
addEventListener('render', function() {
    // Page has rendered something new
});
```

The `oc.pageReady` function is used call code when the page and scripts are ready. The function returns a promise that is resolved after all the page scripts have loaded, or immediately if they are already loaded.

```js
oc.pageReady().then(() => {
    // Page has finished loading scripts
});
```

The `oc.waitFor` is another useful function that will wait for an object or variable to exist. The function returns a promise that is resolved when the variable is found.

```js
oc.waitFor(() => window.propName).then(() => [
    // window.propName is now available
]);
```

The second argument provides a timeout interval in milliseconds, the following will stop waiting after two seconds.

```js
oc.waitFor(() => window.propName, 2000).then(() => {
    console.log('Found the variable!')
}).catch(() => {
    console.error('Gave up waiting...')
});
```

### Inline Script Elements

The turbo router maintains the scripts within the `<head>` tag of the page by comparing the differences. If you use script tags in the `<body>` tag then the script will be executed every time the page renders, which may be undesirable.

You may include `data-turbo-eval="false"` to only allow the script to be executed on the first page load. The script will not be called for any PJAX requests.

```html
<body>
    <script data-turbo-eval="false" src="{{ ['@framework.bundle']|theme }}"></script>
</body>
```
::: tip
If you are placing scripts in the `<body>` tag for performance reasons, consider moving it to the `<head>` tag and using `<script defer>` instead.
:::

To execute inline JavaScript code only once, regardless of first page load or PJAX request, set a unique value to the `data-turbo-eval-once` attribute. The unique value (e.g `myAjaxPromise`) is used to determine if the script has been seen before.

```html
<script data-turbo-eval-once="myAjaxPromise">
    // This script will run once only
    addEventListener('ajax:promise', function(event) {
        //
    });
</script>
```

### Making Controls Idempotent

::: aside
October CMS provides a complimentary library that is used to make building [idempotent controls](./hot-controls.md) easy.
:::

When a page visit occurs and JavaScript components are initialized, it is important that these function are idempotent. In simple terms, an idempotent function is safe to apply multiple times without changing the result beyond its initial application.

One technique for making a function idempotent is to keep track of whether you've already performed it by adding a value to the `dataset` property on each processed element. This is useful for external scripts.

```js
addEventListener('page:loaded', function() {
    // Find my control
    var myControl = document.querySelector('.my-control');

    // Check if control has already been initialized
    if (!myControl.dataset.hasMyControl) {
        myControl.dataset.hasMyControl = true;

        // Initialize since this is the first time
        initializeMyControl(myControl);
    }
});
```

As general advice, a simpler approach is to allow the function to run multiple times and apply idempotence techniques internally. For example, check to see if a menu divider already exists first before creating a new one.

### Disposing Controls

In some cases you may bind global events for a specific page only, for example, binding a hot key to a certain action.

```js
addEventListener('keydown', myKeyDownFunction);
```

To prevent this event from leaking to other pages, you should remove the event using the `page:unload` method, which will destroy any events and controls. The event can be used once to dispose of controls and events safely.

```js
addEventListener('page:unload', function() {
    removeEventListener('keydown', myKeyDownFunction);
}, { once: true });
```

::: tip
October CMS includes a complimentary library for [building disposable controls](./hot-controls.md).
:::

### Pause Rendering

If you would like to animate some elements like dropdowns or off-canvas menus before loading a new page, you can pause the `page:before-render` event by preventing the default behavior and resuming it with the `resume()` function in the event detail.

```js
addEventListener('page:before-render', async (event) => {
    event.preventDefault();

    await animateOut();

    event.detail.resume();
});
```

::: warning
Keep in mind that the **page:before-render** event may fire twice, once from cache and once again after requesting the new page content.
:::

## Global Events

The AJAX framework triggers several events during the navigation life cycle and page responses. The events are usually triggered on the `document` object with details available on the `event.detail` property.

Event | Description
------------- | -------------
**render** | triggered when the page updates via PJAX or AJAX.
**page:click** | triggered when a turbo-routed link is clicked.
**page:before-visit** | triggered before visiting a location, except when navigating using browser history.
**page:visit** | triggered after a clicked visit starts.
**page:request-start** | triggered before the request for a page.
**page:request-end** | triggered after the page request ends.
**page:before-cache** | triggered before a page is cached.
**page:before-render** | triggered before the page content is rendered.
**page:render** | triggered after the page is rendered. This is fired twice, once from cache and once again after requesting the new page content.
**page:load** | triggered once after the initial page load and again every time a page is visited.
**page:loaded** | identical to `page:load` except will wait for all newly added scripts to load.
**page:updated** | similar to `DOMContentLoaded` except triggered only when a page is visited.
**page:unload** | called when a previously loaded page should be disposed.

## Usage Examples

The following JavaScript will run every time a page loads, including scripts.

```js
addEventListener('page:loaded', function() {
    // ...
});
```

## Working with Hot Reloading

In some cases, the turbo router may interfere when you are developing your website with hot reloading or browser sync technology, such as with [Laravel Mix](https://laravel-mix.com/) in development mode using `laravel-mix & browsersync`. To overcome this add the following code to your webpack `browserSync` configuration.

```js
snippetOptions: {
    rule: {
        match: /<\/head>/i,
        fn: function (snippet, match) {
            return '<meta name="turbo-visit-control" content="disable" />';
        }
   }
}
```

#### See Also

::: also
* [Observable Controls](./hot-controls.md)
:::
