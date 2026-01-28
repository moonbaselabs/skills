---
name: laravel-database
description: Laravel Eloquent ORM and database conventions. Use when creating models, defining relationships, writing migrations, adding query scopes, or setting up factories and seeders. Covers model naming, table naming, primary/foreign keys, relationship methods, Scope attribute, migration best practices, and database seeding for developer onboarding.
---

# Laravel Eloquent & Database

## Models

- **DO** use unguarded models
- **Naming**: Singular PascalCase (`User`, `OrderItem`)
- **Tables**: Plural snake_case - Laravel convention (`users`, `order_items`)
- **Primary keys**: Use `id` unless there's a strong reason not to
- **Foreign keys**: Singular model name + `_id` (`user_id`, `order_item_id`)

## Relationships

- **Method names**: camelCase, descriptive of the relationship

| Relationship | Naming | Example |
|--------------|--------|---------|
| hasMany | plural | `comments()` |
| hasOne | singular | `profile()` |
| belongsTo | singular | `user()`, `category()` |
| Many-to-many | plural | `roles()`, `tags()` |

## Query Scopes

- **DO** use the `#[Scope]` attribute to define query scopes
- **Call without prefix**: `User::active()`
- **DO** keep scopes focused on a single filter

```php
use Illuminate\Database\Eloquent\Attributes\Scope;

#[Scope]
public function active(Builder $query): void
{
    $query->where('is_active', true);
}

// Usage
User::active()->get();
```

## Migrations

- **Naming**: Use descriptive names
  - `create_users_table`
  - `add_status_to_orders_table`
- **DO** always define `down()` method for reversibility
- **DO** add indexes for frequently queried columns and foreign keys

## Factories & Seeders

- **DO** create factories for all models
- **DO** set up seeders so `php artisan db:seed` provides a working database for new developers
