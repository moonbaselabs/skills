---
name: laravel-conventions
description: Core Laravel conventions and patterns. Use when creating routes, controllers, config files, artisan commands, policies, translations, or organizing services. Covers Laravel's documented way, facades, mass assignment, route naming, thin controllers with fat models, controller CRUD methods, configuration structure, command output patterns, authorization policies, the __() function, and service directory organization.
---

# Laravel Conventions

## Core Principle

**Follow Laravel conventions first.** If Laravel has a documented way to do something, use it. Only deviate when you have a clear justification.

## General

- **Mass assignment**: Bypass protection, only use validated inputs
- **Facades**: Use over dependency injection
- **Actions/DTOs**: Refrain from using unless functionality is reused in 3+ places
- **Formatting**: Use Laravel Pint

## Authorization

- **DO** authorize requests using route middleware

### Policies

- **Naming**: camelCase (`Gate::define('editPost', ...)`)
- **Methods**: Use CRUD words, but `view` instead of `show`

## Routes

**Naming conventions**: See `laravel-naming` skill for URL, route name, and parameter casing.

- **Controller binding**: Use array tuple syntax `[Controller::class, 'method']`

```php
Route::get('/user-profile/{userId}', [UserProfileController::class, 'show'])
    ->name('user_profile.show');
```

## Controllers

- **Naming**: Singular resource name + `Controller` (`PostController`)
- **Methods**: Stick to CRUD: `index`, `create`, `store`, `show`, `edit`, `update`, `destroy`, `restore`
- **Non-CRUD actions**: Extract to new controllers (excluding `restore`)
- **Thin controllers**: Controllers validate input and delegate to models — no business logic
- **DO NOT** add `protected` or `private` helper methods to controllers
- **DO** move persistence and domain logic to methods on the relevant model

```php
// BAD: Helper method on controller
class PostController extends Controller
{
    public function update(PostRequest $request, Post $post)
    {
        $this->syncTags($post, $request->validated('tags', []));
    }

    protected function syncTags(Post $post, array $tags): void
    {
        // persistence logic...
    }
}

// GOOD: Controller validates, model persists
class PostController extends Controller
{
    public function update(PostRequest $request, Post $post)
    {
        $post->syncTags($request->validated('tags', []));
    }
}
```

## Configuration

| Element | Convention | Example |
|---------|------------|---------|
| Files | kebab-case | `pdf-generator.php` |
| Keys | snake_case | `chrome_path` |

- **DO** add service configs to `config/services.php` - don't create new files
- **DO** use `config()` helper
- **DO NOT** use `env()` outside config files

## Artisan Commands

- **Naming**: kebab-case (`delete-old-records`)
- **DO** always provide feedback: `$this->comment('All ok!')`
- **DO** show progress for loops, summary at end
- **DO** put output BEFORE processing item (easier debugging):

```php
$items->each(function(Item $item) {
    $this->info("Processing item id `{$item->id}`...");
    $this->processItem($item);
});

$this->comment("Processed {$items->count()} items.");
```

## API Routing

- **Resource names**: Plural (`/errors`)
- **URLs**: kebab-case (`/error-occurrences`)
- **Nesting**: Limit depth for simplicity

```php
// Both are acceptable
/error-occurrences/1
/errors/1/occurrences
```

## Error Handling

- **DO** let exceptions bubble up to Laravel's exception handler by default
- **DO** create custom exceptions for domain-specific errors that need special handling
- **Naming**: Descriptive name + `Exception` suffix (`InsufficientFundsException`)
- **DO** use `abort()` helpers in controllers for HTTP errors:
  - `abort(404)`
  - `abort_if()`
  - `abort_unless()`
- **DO NOT** over-log - bug tracking tools provide error context

## Translations

- **DO** use `__()` function over `@lang` directive

## Service Providers & Project Structure

- **DO** register bindings in `AppServiceProvider` - keep it simple
- **DO NOT** use a generic "Services" grab-bag directory
- **DO** create purpose-specific directories in `/app` for each service:
  - `app/Acuity/` - Acuity scheduling integration
  - `app/Analytics/` - Analytics services
  - `app/Images/` - Image processing
  - `app/GiftCodes/` - Gift code handling

## Code Quality Checklist

- Use typed properties over docblocks
- Prefer early returns over nested if/else
- Use constructor property promotion when all properties can be promoted
- Avoid `else` statements when possible
- Use string interpolation over concatenation
- Always use curly braces for control structures
