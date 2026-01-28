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

## Test Strategy

- **DO** prefer holistic feature tests over unit tests
- **DO** prefer fakes over mocks
- **DO** fake services using Laravel Facades
- **DO** keep test classes in same file when possible

## Test Structure

Follow the **arrange-act-assert** pattern:

```php
public function test_user_can_view_their_profile(): void
{
    // Arrange
    $user = User::factory()->create();

    // Act
    $response = $this->actingAs($user)->get('/profile');

    // Assert
    $response->assertOk();
    $response->assertSee($user->name);
}
```
