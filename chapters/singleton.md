
# The Singleton Pattern

* The Singleton pattern is a creational design pattern.
* Ensures a class has only one instance throughout the application's lifetime.
* Provides a global point of access to that instance.

## Key Characteristics

**Single Instance**: No matter how many times you try to instantiate the class, you always get the same object.

**Global Access**: The instance is accessible from anywhere in your application through a static method.

**Lazy Initialization**: The instance is typically created only when it's first needed, not at application startup.

## When to Use It

* For resources that should be shared across your application, such as
  * database connections
  * configuration managers
  * loggers
  * cache handlers 

## Cons 

* Should be used sparingly as it can introduce global state and make testing more difficult.


## PHP Example

```php
<?php

class Database {

    private static ?Database $instance = null;
    private PDO $connection;
    
    // Private constructor prevents direct instantiation
    private function __construct() {
        $host = 'localhost';
        $dbname = 'myapp';
        $username = 'root';
        $password = 'secret';
        
        $this->connection = new PDO(
            "mysql:host=$host;dbname=$dbname",
            $username,
            $password,
            [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION]
        );
    }
    
    // Prevent cloning of the instance (single instance)
    private function __clone() {}
    
    // Prevent unserialization of the instance
    public function __wakeup() {
        throw new Exception("Cannot unserialize singleton");
    }
    
    // Static method to get the single instance 
    public static function getInstance(): Database {
        if (self::$instance === null) {
            self::$instance = new Database();
        }
        return self::$instance;
    }
    
    public function getConnection(): PDO {
        return $this->connection;
    }
}

// Usage
$db1 = Database::getInstance();
$db2 = Database::getInstance();

// Both variables reference the same instance
var_dump($db1 === $db2); // outputs: bool(true)

// Use the connection
$stmt = $db1->getConnection()->prepare("SELECT * FROM users WHERE id = ?");
$stmt->execute([1]);
$user = $stmt->fetch();
```

## Important Considerations

The private constructor prevents creating instances with `new Database()`, the private `__clone()` method prevents copying the instance with `clone`, and preventing unserialization ensures the singleton can't be duplicated through deserialization. The static `$instance` property holds the single instance, and `getInstance()` creates the instance only once and returns it on subsequent calls.

While Singletons can be useful, modern PHP development often favors dependency injection and service containers over the Singleton pattern for better testability and flexibility.
