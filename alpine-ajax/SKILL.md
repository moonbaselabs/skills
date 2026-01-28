---
name: alpine-ajax
description: Implements Alpine AJAX partial page updates with x-target, x-merge, x-sync directives and $ajax helper. Use when building AJAX-powered interactions in Alpine.js projects, handling form submissions without page reloads, or implementing patterns like infinite scroll, live search, notifications, and modal dialogs.
---

# Alpine AJAX

Add AJAX capabilities to Alpine.js with partial page updates and progressive enhancement.

## Installation

### Via CDN

Include before Alpine's core JS:

```html
<script defer src="https://cdn.jsdelivr.net/npm/@imacrayon/alpine-ajax@0.x.x/dist/cdn.min.js"></script>
<script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>
```

### Via NPM

```bash
npm i @imacrayon/alpine-ajax
```

```js
import Alpine from 'alpinejs'
import ajax from '@imacrayon/alpine-ajax'

window.Alpine = Alpine
Alpine.plugin(ajax)
```

## Core Concepts

Build your UI to work without JavaScript first (progressive enhancement). Links and forms should function normally, then add AJAX behavior on top.

## Directives

### x-target

Add to forms or links to enable AJAX. The value is the `id` of the element to update.

```html
<!-- Basic usage: form targets itself -->
<form x-target id="my_form" method="post" action="/submit">
  <button>Submit</button>
</form>

<!-- Target a different element -->
<form x-target="comments" method="post" action="/comment">
  <input name="text" required />
  <button>Submit</button>
</form>
<ul id="comments">...</ul>

<!-- Multiple targets (space-separated) -->
<form x-target="comments comments_count" method="post" action="/comment">
  ...
</form>

<!-- Links work too -->
<a href="/contacts/1" x-target="contact_details">View Contact</a>
<div id="contact_details">...</div>
```

#### HTTP Methods

Forms support all HTTP methods via the `method` attribute:

```html
<form x-target method="delete" action="/contacts/1">
  <button>Delete</button>
</form>

<form x-target method="put" action="/contacts/1">
  <input name="name" value="Updated Name">
  <button>Update</button>
</form>
```

#### Target Aliases

When IDs don't match between current page and response:

```html
<!-- Replace #modal_body with #page_body from response -->
<a x-target="modal_body:page_body" href="/content">Load</a>

<!-- Shorthand when targeting self with alias -->
<form x-target=":alias_id" id="my_form" ...>
```

#### Status Code Modifiers

```html
<!-- Different targets based on response status -->
<form x-target="content" x-target.422="form" x-target.error="errors" ...>

<!-- Full page reload on redirect away -->
<form x-target="login" x-target.away="_top" id="login" method="post" action="/login">
```

Available modifiers:
- `.422`, `.4xx`, `.5xx` - Specific or class status codes
- `.back` - Response redirected back to same page
- `.away` - Response redirected to different page
- `.error` - 400 or 500 class status codes

#### Special Targets

- `_top` - Full page reload
- `_none` - Do nothing

#### Other Modifiers

- `x-target.url` - Update browser URL when request completes
- `x-target.nofocus` - Disable autofocus behavior

#### Dynamic Targets

```html
<template x-for="item in items" :key="item.id">
  <div :id="'item_'+item.id">
    <form x-target:dynamic="'item_'+item.id" :action="'/items/'+item.id" method="post">
      <button>Edit</button>
    </form>
  </div>
</template>
```

#### Disable AJAX Per Button

```html
<form x-target id="checkout" method="post" action="/checkout">
  <button name="action" value="update">Update</button>
  <button formnoajax name="action" value="purchase">Purchase</button>
</form>
```

### x-merge

Controls how incoming content merges with the target. Add to the **target element**.

```html
<ul id="messages" x-merge="append">
  <li>First message</li>
</ul>
```

Strategies:
- `replace` - (Default) Replace entire element
- `update` - Replace only inner content
- `before` - Insert before target
- `after` - Insert after target
- `prepend` - Prepend to target's content
- `append` - Append to target's content
- `morph` - Intelligent DOM diffing (requires Alpine Morph plugin)

#### Morphing

Requires Alpine Morph plugin installed before Alpine AJAX:

```js
import Alpine from 'alpinejs'
import morph from '@alpinejs/morph'
import ajax from '@imacrayon/alpine-ajax'

Alpine.plugin(morph)
Alpine.plugin(ajax)
```

```html
<div id="content" x-merge="morph">...</div>
```

#### View Transitions

```html
<div id="content" x-merge.transition>...</div>
```

### x-sync

Elements with `x-sync` update whenever the server sends a matching element, even if not targeted.

```html
<!-- Always updates when server includes id="notifications" -->
<ul x-sync id="notifications" role="status"></ul>
```

Useful for notification counters, flash messages, or global state that should update on any request.

### x-autofocus

Restores keyboard focus when content changes. Add to elements that should receive focus after AJAX updates.

```html
<button x-autofocus>Like</button>
```

The standard `autofocus` attribute also works. When both exist, `x-autofocus` takes priority.

### x-headers

Add custom request headers:

```html
<form x-headers="{'X-Custom': 'value'}" x-target ...>
```

Default headers sent with every request:
- `X-Alpine-Request: true`
- `X-Alpine-Target: <space-separated target IDs>`

## $ajax Magic Helper

Programmatically issue AJAX requests:

```html
<div id="email_field"
     x-data="{ email: '' }"
     @change="$ajax('/validate', { method: 'post', body: { email } })">
  <input type="email" x-model="email">
</div>
```

### Options

| Option | Default | Description |
|--------|---------|-------------|
| `method` | `'GET'` | Request method |
| `target` | `''` | Target element ID |
| `targets` | `[]` | Array of target IDs |
| `body` | `{}` | Request body |
| `focus` | `false` | Enable autofocus behavior |
| `sync` | `false` | Include x-sync targets |
| `headers` | `{}` | Additional headers |

```html
<!-- Lazy load content on init -->
<article id="post" x-init="$ajax('/posts/1')">
  <div class="loader">Loading...</div>
</article>
```

## Events

Listen during AJAX lifecycle:

| Event | Description |
|-------|-------------|
| `ajax:before` | Before request. Cancel with `$event.preventDefault()` |
| `ajax:send` | Request issued. `$event.detail` has request options |
| `ajax:redirect` | 300 class response |
| `ajax:success` | 200 or 300 class response |
| `ajax:error` | 400 or 500 class response |
| `ajax:sent` | After response received |
| `ajax:missing` | Target not found in response |
| `ajax:merge` | Before content merge. Cancel to override |
| `ajax:merged` | After content merged |
| `ajax:after` | After all merging settled |

```html
<!-- Confirm before delete -->
<form x-target @ajax:before="confirm('Delete?') || $event.preventDefault()">
  <button>Delete</button>
</form>
```

## Loading States

During AJAX requests:
- Submit buttons are automatically disabled
- `aria-busy="true"` is set on all targets

```css
/* Style loading state */
[aria-busy="true"] {
  opacity: 0.5;
}
```

## Configuration

```js
import ajax from '@imacrayon/alpine-ajax'

Alpine.plugin(ajax.configure({
  headers: { 'X-CSRF-Token': 'token-value' },
  mergeStrategy: 'morph'
}))
```

| Option | Default | Description |
|--------|---------|-------------|
| `headers` | `{}` | Headers for every request |
| `mergeStrategy` | `'replace'` | Default merge strategy |

## Common Patterns

### Toggle Button

```html
<!-- Server returns opposite state -->
<form id="like" x-target method="post" action="/posts/1/like">
  <button x-autofocus>Like</button>
</form>
```

### Form with Validation Errors

```html
<form x-target="form" x-target.back="form" id="form" method="post" action="/submit">
  <input name="email" required>
  <button>Submit</button>
</form>
```

### Notifications with x-sync

```html
<ul x-sync id="notifications" x-merge="prepend" role="status"></ul>

<!-- Any AJAX response including id="notifications" updates this -->
<form x-target="other_content" method="post" action="/action">
  <button>Submit</button>
</form>
```

### Inline Validation

```html
<form x-data="{ email: '' }">
  <div id="email_field" @change="$ajax('/validate-email', {
    method: 'post',
    body: { email }
  })">
    <input type="email" x-model="email">
  </div>
</form>
```

### Lazy Loading

```html
<article id="post" x-init="$ajax('/posts/1')">
  <div aria-label="Loading">...</div>
</article>
```

**More patterns**: See [patterns.md](patterns.md) for Dialog/Modal, Instant Search, Infinite Scroll, Server Events, Delete with Confirmation, and Filterable Content.

## Server Response Requirements

- Response must contain elements with matching IDs for each target
- For redirects, server should return appropriate 300 status codes
- Include CSRF tokens in headers if required by your backend

## Reference

See [reference.md](reference.md) for:
- **Accessibility** - Live regions, toggle states, validation errors, focus management
- **Plugin Integrations** - Load order, Alpine Morph, Alpine Intersect
- **Progressive Enhancement** - Hide JS-only elements, programmatic form submission
