# DEPENDENCY INVERSION PRINCIPLE (DIP) TUTORIAL

The Dependency Inversion Principle is the "D" in SOLID principles.

It states that:
1. High-level modules should not depend on low-level modules.
   Both should depend on abstractions (interfaces).
2. Abstractions should not depend on details.
   Details should depend on abstractions.

WHY IS THIS IMPORTANT?
- Makes code more flexible and maintainable
- Allows easy switching between implementations
- Reduces tight coupling between classes
- Makes testing easier with mock implementations

IN THIS EXAMPLE:
- NotificationService (high-level) doesn't depend on EmailService, SMSService, etc.
- Instead, it depends on the NotificationSender interface (abstraction)
- Each concrete service (EmailService, SMSService, etc.) implements the interface
- You can easily swap implementations without changing NotificationService

BENEFITS DEMONSTRATED:
1. FLEXIBILITY: Add new notification types (Slack, WhatsApp) without modifying existing code
2. TESTABILITY: Create mock NotificationSender for unit testing
3. MAINTAINABILITY: NotificationService is independent of notification implementation details
4. REUSABILITY: Any class can use the NotificationSender interface

```php
/**
 * NotificationSender Interface
 * 
 * This is the ABSTRACTION that both high-level and low-level modules depend on.
 * It defines the contract that all notification implementations must follow.
 * By coding to this interface, we decouple the sender from specific implementations.
 */
interface NoticicationSender {
	/**
	 * Send a notification message
	 * 
	 * @param String $message The message to send
	 */
	public function send(String $message);
}

/**
 * NotificationService Class
 * 
 * This is the HIGH-LEVEL module.
 * Notice it depends on the NotificationSender INTERFACE, not concrete classes.
 * This is dependency inversion - the service depends on an abstraction, not implementations.
 * 
 * Key Point: This class doesn't care HOW the notification is sent,
 * only THAT it gets sent according to the interface contract.
 */
class NotificationService {
	private NoticicationSender $notificationSender;

	/**
	 * Constructor using dependency injection
	 * 
	 * The dependency (NotificationSender) is injected from outside.
	 * This is constructor injection - a form of inversion of control.
	 * 
	 * @param NoticicationSender $notificationSender Any class implementing the interface
	 */
	public function __construct(NoticicationSender $notificationSender) {
		$this->notificationSender = $notificationSender;
	}

	/**
	 * Send a notification using the injected sender
	 * 
	 * @param String $message The message to send
	 */
	public function sendNotification(String $message) {
		$this->notificationSender->send($message);
	}
}

/**
 * Below are LOW-LEVEL modules (concrete implementations). 
 * They implement the NotificationSender interface, allowing them to be used interchangeably.
 * Each can be swapped in NotificationService without any changes to the service itself.
 */

class EmailService implements NoticicationSender {
	/**
	 * Send notification via email
	 * 
	 * @param String $message The message to send
	 */
	public function send(String $message) {
		echo "Sending email with message: " . $message;
	}
}

class SMSService implements NoticicationSender {
	/**
	 * Send notification via SMS
	 * 
	 * @param String $message The message to send
	 */
	public function send(String $message) {
		echo "Sending SMS with message: " . $message;
	}
}

class PushNotificationService implements NoticicationSender {
	/**
	 * Send notification via push notification
	 * 
	 * @param String $message The message to send
	 */
	public function send(String $message) {
		echo "Sending push notification with message: " . $message;
	}
}

// USAGE EXAMPLES

class NotificationFactory {
	/**
	 * Create a notification sender based on the type
	 * 
	 * @param String $type The type of notification (email, sms, push)
	 * @return NoticicationSender An instance of a class implementing the interface
	 * @throws Exception If an invalid type is provided
	 */
	public static function createNotificationSender(String $type): NoticicationSender {
		if ($type === "email") {
			return new EmailService();
		} elseif ($type === "sms") {
			return new SMSService();
		} elseif ($type === "push") {
			return new PushNotificationService();
		} else {
			throw new Exception("Invalid notification type");
		}
	}
}

$type_from_request = "email"; // This could come from user input, config, etc.
$message_from_request = "Hello World!";

$notificationSender = NotificationFactory::createNotificationSender($type_from_request);
$notificationService = new NotificationService($notificationSender);
$notificationService->sendNotification($message_from_request);
```
