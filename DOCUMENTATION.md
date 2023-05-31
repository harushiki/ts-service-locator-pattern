# TypeScript Service Locator Pattern Documentation

## Overview

The TypeScript service locator pattern is a design pattern that enables the central management and resolution of dependencies in an application. It provides a way to decouple components and allows for easy swapping of implementations based on different environments or testing scenarios.

This documentation outlines the implementation of the service locator pattern with the following features:

-   Declaration of singleton dependencies based on different environments.
-   Support for lazy and non-lazy singletons.
-   Resolving objects with generics.
-   Running tests with Mocha as a test environment.
-   Handling chain dependencies.

## Prerequisites

To use the service locator pattern in your TypeScript project, ensure that you have the following prerequisites:
Node.js and npm (Node Package Manager) installed on your machine.

## Installation

To get started, follow these steps to set up the service locator pattern in your TypeScript project:

Open a terminal and navigate to your project's root directory.

Initialize a new npm project by running the following command and following the prompts:

```shell
npm init
```

Install the required dependencies by executing the following command:

```shell
npm install --save-dev mocha ts-node chai @types/chai
```

## Implementation

### Step 1: Create the Abstract Classes and Implement Concrete Classes

Create the necessary abstract and concrete classes representing the dependencies and their relationships. For example:

```typescript
abstract class EmailService {
    abstract sendEmail(recipient: string, subject: string, body: string): void;
}

class SendGridEmailService extends EmailService {
    sendEmail(recipient: string, subject: string, body: string): void {
        // Implementation for sending emails using SendGrid API
    }
}

class Logger {
    log(message: string): void {
        // Implementation
    }
}

class NotificationService {
    constructor(private emailService: EmailService, private logger: Logger) {}

    sendNotification(recipient: string, message: string): void {
        this.emailService.sendEmail(recipient, "Notification", message);
        this.logger.log(`Notification sent to ${recipient}: ${message}`);
    }
}
```

### Step 2: Create the Service Locator

Create a service locator class to manage the dependencies and their resolution. Here's an example of a basic service locator implementation:

```typescript
class ServiceLocator {
    private static container: { [key: string]: any } = {};

    static registerSingleton<T>(
        key: string,
        implementation: new () => T
    ): void {
        ServiceLocator.container[key] = new implementation();
    }

    static registerSingletonFactory<T>(key: string, factory: () => T): void {
        ServiceLocator.container[key] = factory();
    }

    static resolve<T>(key: string): T {
        const service = ServiceLocator.container[key];
        if (!service) {
            throw new Error(`Service ${key} not registered.`);
        }
        return service as T;
    }
}
```

The ServiceLocator class maintains a container to store the registered dependencies. It provides methods to register singletons with their corresponding keys and to resolve dependencies based on the provided key.

### Step 3: Configure the Service Locator

Configure the service locator based on the environment or testing scenario. Use the registerSingleton or registerSingletonFactory methods to register the dependencies. For example:

```typescript
// In production environment
ServiceLocator.registerSingleton("EmailService", SendGridEmailService);
ServiceLocator.registerSingleton("Logger", Logger);

// In staging environment
ServiceLocator.registerSingleton("EmailService", SingleRecipientEmailService);
ServiceLocator.registerSingleton("Logger", Logger);

// In test environment
ServiceLocator.registerSingleton("EmailService", FakeEmailService);
ServiceLocator.registerSingleton("Logger", FakeLogger);
```

### Step 4: Resolve Dependencies

To use the dependencies, resolve them from the service locator. Here's an example:

```typescript
const emailService = ServiceLocator.resolve<EmailService>("EmailService");
emailService.sendEmail(
    "recipient@example.com",
    "Hello",
    "This is the body of the email."
);

const logger = ServiceLocator.resolve<Logger>("Logger");
logger.log("Log message");
```

The resolve method retrieves the registered singleton for the specified key and returns the resolved instance.

### Step 5: Resolve Chain Dependencies

If you have chain dependencies, where one class depends on another, you can resolve them by registering and resolving each dependency individually. Here's an example:

```typescript
ServiceLocator.registerSingletonFactory("NotificationService", () => {
    const emailService = ServiceLocator.resolve<EmailService>("EmailService");
    const logger = ServiceLocator.resolve<Logger>("Logger");
    return new NotificationService(emailService, logger);
});

const notificationService = ServiceLocator.resolve<NotificationService>(
    "NotificationService"
);
notificationService.sendNotification(
    "recipient@example.com",
    "Hello, this is a notification."
);
```

In this example, the NotificationService depends on both EmailService and Logger. By registering a factory function that resolves the dependencies individually, you can obtain a fully instantiated NotificationService instance with its chain dependencies properly injected.

### Running Tests with Mocha

To run tests using Mocha as the test environment, follow these steps:

1. Create a test file (e.g., emailService.test.ts) to write your Mocha test cases.

2. Install the necessary testing dependencies by executing the following command:

```shell
npm install --save-dev mocha ts-node chai @types/chai
```

3. Configure Mocha in your package.json file by adding the following script:

```json
"scripts": {
  "test": "mocha --require ts-node/register 'test/**/*.test.ts'"
}
```

4. Write your Mocha test cases in the test file. Use the service locator to resolve the dependencies and perform the required assertions.

5. Run the tests by executing the following command:

```shell
npm test
```

Mocha will discover and execute the test files specified in the test script.

## Conclusion

The TypeScript service locator pattern provides a flexible way to manage and resolve dependencies in your application. By centralizing the dependency registration and resolution logic, you can easily switch implementations based on different environments, handle lazy and non-lazy singletons, utilize generics, run tests with Mocha, and handle chain dependencies.

Feel free to customize the implementation and configuration based on your specific requirements and project structure.
