# Alpine AJAX Patterns

Detailed implementation patterns for common AJAX interactions.

## Dialog/Modal

Open a modal before loading content using event dispatch:

```html
<ul x-init @ajax:before="$dispatch('dialog:open')">
  <li><a href="/contacts/1" x-target="contact">View Contact</a></li>
</ul>

<dialog x-init @dialog:open.window="$el.showModal()">
  <div id="contact"></div>
  <form method="dialog"><button>Close</button></form>
</dialog>
```

Use `x-target.nofocus` when a dialog component handles its own focus:

```html
<a href="/preview" x-target.nofocus="dialog_content" @ajax:before="$dispatch('dialog:open')">Preview</a>
```

## Instant Search

Live search with debounced input:

```html
<form x-target="results" action="/search" role="search">
  <input type="search" name="q"
         @input.debounce="$el.form.requestSubmit()"
         @search="$el.form.requestSubmit()">
  <button x-show="false">Search</button>
</form>
<div id="results">...</div>
```

The `@search` event handles clearing the input via the "x" button. `x-show="false"` hides the submit button when JS loads while keeping the form functional without JS.

## Infinite Scroll

Requires Alpine Intersect plugin:

```html
<tbody id="records" x-merge="append">
  ...
</tbody>
<div id="pagination" x-init x-intersect="$ajax('/items?page=2', { target: 'records pagination' })">
  <a href="/items?page=2" x-show="false">Next</a>
</div>
```

Server response updates both `records` (appended) and `pagination` (with next page URL).

## Server Events

Decouple components using custom events with `x-sync`:

```html
<!-- List refreshes when comment:created fires -->
<ul x-init @comment:created.window="$ajax('/comments')" id="comments">...</ul>

<!-- Event bus element -->
<div role="status">
  <div x-sync id="server_events"></div>
</div>

<!-- Form only updates itself -->
<form id="comment_form" x-target method="post" action="/comments">
  <textarea name="body" x-autofocus></textarea>
  <button>Submit</button>
</form>
```

Server response triggers the event:

```html
<div x-sync id="server_events">
  <div x-init="$dispatch('comment:created')">Comment added!</div>
</div>
```

## Delete with Confirmation

Add confirmation at the container level:

```html
<tbody id="contacts" x-init @submit="confirm('Are you sure?') || $event.preventDefault()">
  <tr>
    <td>Name</td>
    <td>
      <form method="delete" action="/contacts/1" x-target="contacts">
        <button>Delete</button>
      </form>
    </td>
  </tr>
</tbody>
```

## Filterable Content

Use `morph` to preserve focus state on filter buttons:

```html
<div id="contacts" x-merge="morph">
  <form action="/contacts" x-target="contacts">
    <button name="status" value="Active" aria-pressed="false">Active</button>
    <button name="status" value="Inactive" aria-pressed="false">Inactive</button>
  </form>
  <table>...</table>
</div>
```

Server returns updated `aria-pressed` states and filtered content.
