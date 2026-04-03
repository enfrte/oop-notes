# Data Transfer Objects (DTOs)

**What is a DTO?**

A Data Transfer Object is a simple object designed to carry data between different parts of an application (e.g., from APIs, browser requests, etc.). It structures data as an object rather than using unstructured arrays with arbitrary string keys that are never enforced, typed, or validated..

**The Problem DTOs Solve**

Let's demonstrate this with an email validation example using two different APIs (Emailable and Abstract API). Each API returns differently structured data:
- One uses `state` and `score`
- The other uses `deliverability` and `quality_score`

When switching between APIs, code breaks because it's accessing array keys that don't exist in the other service. This forces ugly conditional checks throughout the application.

**The DTO Solution**

Create a simple class that standardizes the data structure:
```php
class EmailValidationResult {
    public function __construct(public readonly int $score, public readonly bool $isDeliverable) {}
}
```

Each API service converts its unique response format into this common DTO format internally, so the rest of the application always works with a consistent, typed object.

**Key Benefits**
- **Type safety**: Properties can be type-hinted
- **Consistency**: Same structure regardless of data source
- **Immutability**: DTOs are typically read-only (using `readonly`)
- **No business logic**: Just a "dumb" data container

**When to Use DTOs**
Use them when you're passing the same data structure to multiple methods/classes, especially when dealing with external APIs or complex request/response data.

**Handy method to have inside DTO**

```php
public function toArray(): array
{
    $properties = get_object_vars($this);
    $result = [];

    foreach ($properties as $key => $value) {
        if ($value instanceof self) {
            $result[$key] = $value->toArray();
        } else {
            $result[$key] = $value;
        }
    }

    return $result;
}
```

Note: This only gets other DTOs at the current level. If there is a chance of further DTO nesting, it's best to give all the DTOs something like 

```php
interface Arrayable
{
    public function toArray(): array;
}
```
then check with `if ($value instanceof Arrayable) {`


