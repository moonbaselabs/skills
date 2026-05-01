---
name: laravel-testing
description: Laravel testing standards and patterns. Use when writing PHPUnit tests, feature tests, or unit tests. Covers test naming with test_ prefix, fakes over mocks, arrange-act-assert pattern, and holistic feature test approach.
---

# Laravel Testing

## Framework

- **Use PHPUnit**

## Test Naming

- **DO** prefix test names with `test_`
- **DO** use descriptive test method names
- **NEVER** add comments to tests (test names should be descriptive)

## Test Strategy

- **DO** prefer holistic feature tests over unit tests
- **DO** prefer fakes over mocks
- **DO** fake services using Laravel Facades
- **DO** keep test classes in same file when possible

## Assertion Quality

Assert against behavior and data structures, not rendered text. Text assertions (`assertSee`, `assertSeeText`, regex matches against HTML) are brittle, break on copy changes, and rarely prove the feature actually worked.

- **DO** assert against the database (`assertDatabaseHas`, `assertDatabaseCount`, model state)
- **DO** assert against response data (JSON structure, Inertia props, redirect targets, status codes)
- **DO** assert against side effects (queued jobs, dispatched events, sent notifications, fake assertions)
- **DO** assert authorization and validation outcomes (status codes, session errors, redirects)
- **AVOID** `assertSee` / `assertSeeText` to verify business logic. Reserve them for cases where the rendered string itself is the contract (e.g., a literal error message a user must see)
- **NEVER** test that a label, heading, or piece of static UI copy is present. That tests the template, not the feature.

## Test Structure

Follow the **arrange-act-assert** pattern. Prefer assertions that prove the feature worked, not that the page rendered some text.

```php
public function test_user_can_update_their_profile(): void
{
    // Arrange
    $user = User::factory()->create();

    // Act
    $response = $this->actingAs($user)->patch('/profile', [
        'name' => 'New Name',
    ]);

    // Assert
    $response->assertRedirect('/profile');
    $this->assertSame('New Name', $user->fresh()->name);
}
```
