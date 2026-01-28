---
name: laravel-naming
description: Quick reference for Laravel naming conventions. Use when naming classes, files, routes, database tables, or unsure about casing (PascalCase, camelCase, snake_case, kebab-case). Lookup table for controllers, models, views, jobs, events, listeners, commands, mailables, resources, enums, and foreign keys.
---

# Laravel Naming Quick Reference

## Casing Conventions

| Context | Convention | Example |
|---------|------------|---------|
| Classes | PascalCase | `UserController`, `OrderStatus` |
| Methods/Variables | camelCase | `getUserName`, `$firstName` |
| Route URLs | kebab-case | `/open-source`, `/user-profile` |
| Route names | snake_case | `open_source`, `user_profile.show` |
| Route parameters | camelCase | `{userId}`, `{orderId}` |
| Config files | kebab-case | `pdf-generator.php` |
| Config keys | snake_case | `chrome_path` |
| Artisan commands | kebab-case | `delete-old-records` |
| DB tables | plural snake_case | `users`, `order_items` |
| Models | singular PascalCase | `User`, `OrderItem` |
| Foreign keys | singular_model_id | `user_id`, `order_item_id` |

## File Structure Patterns

| Type | Pattern | Example |
|------|---------|---------|
| Controllers | singular + `Controller` | `PostController` |
| Views | snake_case | `open_source.blade.php` |
| Jobs | action-based | `CreateUser`, `SendEmailNotification` |
| Events | tense-based | `UserRegistering`, `UserRegistered` |
| Listeners | action + `Listener` | `SendInvitationMailListener` |
| Commands | action + `Command` | `PublishScheduledPostsCommand` |
| Mailables | purpose + `Mail` | `AccountActivatedMail` |
| Resources | singular + `Resource` | `UserResource` |
| Enums | descriptive, no prefix | `OrderStatus`, `BookingType` |
