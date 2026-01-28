---
name: laravel-validation
description: Laravel validation rules and Form Requests. Use when adding form validation, creating custom validation rules, or deciding between inline validation and Form Requests. Covers array notation for rules, snake_case custom rules, and when to use Form Requests.
---

# Laravel Validation

## Rule Syntax

- **DO** use array notation for multiple rules (easier for custom rule classes):

```php
public function rules(): array
{
    return [
        'email' => ['required', 'email'],
    ];
}
```

## Custom Validation Rules

- **DO** use snake_case for custom validation rules:

```php
Validator::extend('organisation_type', function ($attribute, $value) {
    return OrganisationType::isValid($value);
});
```

## Inline vs Form Requests

- **DO** prefer inline validation
- **ONLY** use Form Requests when:
  - Large number of fields require validation
  - Validation is reused in multiple controllers
  - Validation is very complex
