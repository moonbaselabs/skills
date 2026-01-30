---
name: php-coding
description: PHP coding standards and best practices. Use when writing PHP code, defining types, creating docblocks, or structuring control flow. Covers PSR-1/2/12 standards, type declarations, nullable syntax, docblock rules, happy path patterns, early returns, string formatting, enums, comments, and whitespace.
---

# PHP Coding Standards

## PSR Standards

- **Follow PSR-1, PSR-2, and PSR-12**
- **camelCase**: Use for non-public-facing strings
- **Nullable syntax**: Use `?string` not `string|null`
- **Void returns**: Always specify `void` when methods return nothing
- **Constructor promotion**: Use when all properties can be promoted
- **Traits**: One trait per line

## Type Declarations

- **DO** use typed properties over docblocks
- **DO** specify return types including `void`
- **DO** use short nullable syntax: `?Type` not `Type|null`
- **DO** document iterables with generics:

```php
/** @return Collection<int, User> */
public function getUsers(): Collection
```

## Docblock Rules

- **DO NOT** use docblocks for fully type-hinted methods (unless description needed)
- **DO** always import classnames in docblocks - never use fully qualified names:

```php
// GOOD
use Illuminate\Support\Uri;
/** @return Uri */

// BAD
/** @return \Illuminate\Support\Uri */
```

- **DO** use one-line docblocks when possible: `/** @var string */`
- **DO** put most common type first in multi-type docblocks:

```php
/** @var Collection|SomeWeirdVendor\Collection */
```

- **DO** add docblocks for all parameters if one parameter needs a docblock
- **DO** specify key and value types for iterables:

```php
/**
 * @param array<int, MyObject> $myArray
 * @param int $typedArgument
 */
function someFunction(array $myArray, int $typedArgument) {}
```

- **DO** use array shape notation for fixed keys, each key on its own line:

```php
/** @return array{
   first: SomeClass,
   second: SomeClass
} */
```

## Control Flow (Happy Path Pattern)

- **Happy path last**: Handle error conditions first, success case last
- **Avoid else**: Use early returns instead of nested conditions
- **Separate conditions**: Prefer multiple if statements over compound conditions
- **Always use curly brackets**: Even for single statements

```php
// GOOD: Happy path last with early returns
if (! $user) {
    return null;
}

if (! $user->isActive()) {
    return null;
}

// Process active user (happy path)...
```

## Ternary Operators

- **Short ternary**: Keep on one line if very short
- **Multi-line ternary**: Each part on own line

```php
// Short - one line OK
$name = $isFoo ? 'foo' : 'bar';

// Multi-line - use instead of else
$condition
    ? $this->doSomething()
    : $this->doSomethingElse();
```

## Strings

- **DO** use string interpolation over concatenation

## Enums

- **DO** use PascalCase for enum values

## Comments

Code should be self-documenting. Comments often become outdated and misleading.

- **DO NOT** add comments that describe what the code does
- **DO** use descriptive variable/function names instead
- **ONLY** add comments to explain WHY something non-obvious is done

```php
// BAD: Comment describes what code does
// Get the failed checks for this site
$checks = $site->checks()->where('status', 'failed')->get();

// GOOD: Descriptive variable name
$failedChecks = $site->checks()->where('status', 'failed')->get();
```

## Whitespace

- **DO** add blank lines between statements for readability
- **Exception**: Sequences of equivalent single-line operations
- **DO NOT** add extra empty lines between `{}` brackets
- **DO** let code "breathe" - avoid cramped formatting
