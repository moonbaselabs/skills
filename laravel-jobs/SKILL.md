---
name: laravel-jobs
description: Laravel queues, jobs, and error handling. Use when creating background jobs, queue workers, or handling exceptions. Covers job naming, idempotency, retry logic with $tries and $backoff, failed() method, custom exceptions, and abort helpers.
---

# Laravel Queues, Jobs & Error Handling

## Job Naming

- **DO** use action-based names: `ProcessPayment`, `SendWelcomeEmail`

## Job Design

- **DO** keep jobs small and focused on a single task
- **DO** make jobs idempotent (safe to retry)
- **DO** use `$tries` and `$backoff` properties for retry logic
- **DO** implement `failed()` method for cleanup on failure

```php
class ProcessPayment implements ShouldQueue
{
    public int $tries = 3;
    public array $backoff = [60, 300, 900];

    public function handle(): void
    {
        // Process payment...
    }

    public function failed(Throwable $exception): void
    {
        // Cleanup or notify...
    }
}
```
