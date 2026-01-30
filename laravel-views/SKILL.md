---
name: laravel-views
description: Laravel Blade templates and UI patterns. Use when writing Blade views, creating forms, or adding interactivity. Covers Blade syntax, standard HTML forms, Alpine AJAX progressive enhancement with x-target, and when to avoid JSON APIs for UI.
---

# Laravel Blade Templates & UI

## Blade Syntax

- **Indentation**: 4 spaces
- **Control structures**: No spaces after directives

```blade
@if($condition)
    Something
@endif
```

## UI Philosophy

Prioritize robust, maintainable UI patterns over complex JavaScript solutions. Prefer existing components over creating new ones.

## Icons

- **DO** use `blade-ui-kit/blade-icons` components for all SVG icons
- **Naming**: `<x-{library}-{icon-name}>` - prefix with icon library name, then icon name

```blade
<x-phosphor-user />
<x-heroicon-o-arrow-right />
```

## Default: Standard HTML Forms

- **DO** use standard HTML form submissions with full page refreshes by default
- **DO** stick to CRUD controllers that redirect after processing
- **DO** use `<x-form>` to generate form markup with CSRF token
- Simple, predictable behavior that's easy to maintain and debug

## Form Inputs

- **DO** use `<x-radio.group>` instead of `<x-select>` when there are 5 or fewer options

## Progressive Enhancement with Alpine AJAX

When a more dynamic UI is warranted:

- **DO** convert standard forms to AJAX by adding the `x-target` attribute (via `@imacrayon/alpine-ajax`)

**For Alpine AJAX details**: See the `alpine-ajax` skill for directives, patterns, and server response requirements.
- **DO** set `x-target` value to match an element's `id` that will be replaced with the response
- **DO** return HTML partials from controllers, not JSON

```blade
{{-- Element to be updated --}}
<ul id="collaborators_list">
    @foreach($collaborators as $collaborator)
        <li>{{ $collaborator->name }}</li>
    @endforeach
</ul>

{{-- AJAX-enabled form targeting that element --}}
<x-form x-target="collaborators_list" method="delete" action="{{ route('campaigns.users.destroy', $campaign) }}">
    <button name="collaborator_id" value="{{ $collaborator->id }}">Remove</button>
</x-form>
```

## Partials

- **DO** ensure element `id` attributes are unique when a partial may be rendered multiple times on the same page
- **DO** pass a unique identifier to the partial and use it to scope IDs (e.g., `id="post_{{ $post->id }}_title"`)

## Shared Form Fields

When create and edit forms share the same fields, extract them into a partial named `_fields.blade.php`:

```blade
{{-- resources/views/posts/_fields.blade.php --}}
<x-input label="Title" name="title" :value="old('title', $post->title ?? '')" />
<x-textarea label="Body" name="body" :value="old('body', $post->body ?? '')" />
```

```blade
{{-- resources/views/posts/create.blade.php --}}
<x-form method="post" action="{{ route('posts.store') }}">
    @include('posts._fields')
    <button>Create Post</button>
</x-form>
```

```blade
{{-- resources/views/posts/edit.blade.php --}}
<x-form method="put" action="{{ route('posts.update', $post) }}">
    @include('posts._fields', ['post' => $post])
    <button>Update Post</button>
</x-form>
```

## Destructive Actions

- **DO** use muted styling (e.g., gray, subtle) for destructive actions when they appear alongside other actions
- **DO** use red/danger styling only when the destructive action stands alone
- **DO** require confirmation via `confirm()` dialog, or make the action easily reversible (e.g., soft delete with undo)

## Avoid JSON APIs for UI

- **DO NOT** use JSON responses for UI interactions - treat as anti-pattern
- **DO** return HTML partials that directly replace page content
- **ONLY** use JSON APIs for third-party integrations or mobile apps, not internal UI
