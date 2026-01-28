# Alpine AJAX Reference

## Accessibility

### Live Regions

Wrap `x-sync` elements in status regions for screen reader announcements:

```html
<div role="status">
  <ul x-sync id="notifications" x-merge="prepend"></ul>
</div>
```

### Toggle States

Use `aria-pressed` for filter/toggle buttons:

```html
<button name="filter" value="active" aria-pressed="true">Active</button>
<button name="filter" value="inactive" aria-pressed="false">Inactive</button>
```

### Validation Errors

Link inputs to error messages with `aria-describedby`:

```html
<div id="email_error" style="color:#cc0000">Email is invalid</div>
<input type="email" name="email" aria-describedby="email_error">
```

### Focus Management

- Use `x-autofocus` on elements that should receive focus after updates
- Use `x-merge="morph"` to preserve focus during content changes
- Use `x-target.nofocus` when handing focus to dialog components

## Plugin Integrations

### Load Order

When using multiple plugins, load in this order:

```html
<!-- CDN -->
<script defer src="https://cdn.jsdelivr.net/npm/@alpinejs/morph@3.x.x/dist/cdn.min.js"></script>
<script defer src="https://cdn.jsdelivr.net/npm/@alpinejs/intersect@3.x.x/dist/cdn.min.js"></script>
<script defer src="https://cdn.jsdelivr.net/npm/@imacrayon/alpine-ajax@0.x.x/dist/cdn.min.js"></script>
<script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>
```

```js
// NPM
import Alpine from 'alpinejs'
import morph from '@alpinejs/morph'
import intersect from '@alpinejs/intersect'
import ajax from '@imacrayon/alpine-ajax'

Alpine.plugin(morph)
Alpine.plugin(intersect)
Alpine.plugin(ajax)
Alpine.start()
```

### Alpine Morph

Required for `x-merge="morph"`. Preserves Alpine state and focus during DOM updates.

### Alpine Intersect

Required for infinite scroll patterns using `x-intersect`.

## Progressive Enhancement

### Hide JS-Only Elements

Use `x-show="false"` to hide elements when JavaScript loads:

```html
<form x-target="results" action="/search">
  <input type="search" name="q" @input.debounce="$el.form.requestSubmit()">
  <button x-show="false">Search</button>
</form>
```

The button works without JS but hides when Alpine loads (search happens on input).

### Programmatic Form Submission

Use `$el.form.requestSubmit()` to trigger AJAX form submission from events:

```html
<select name="sort" @change="$el.form.requestSubmit()">
  <option value="name">Name</option>
  <option value="date">Date</option>
</select>
```

This respects the form's `x-target` and triggers validation, unlike `$el.form.submit()`.
