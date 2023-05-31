# TS Service Locator Pattern

A flexible and decoupled approach for managing dependencies in TypeScript applications. This pattern allows you to register and resolve singleton dependencies based on different environments, support lazy and non-lazy singletons, handle generics, run tests with Mocha, and handle chain dependencies.

## Features:

-   Centralize dependency management with a service locator class.
-   Register singletons for different environments (e.g., production, staging, testing).
-   Support for lazy and non-lazy singleton objects.
-   Resolve dependencies with type hints and generics.
-   Seamlessly integrate with Mocha to run tests.
-   Handle chain dependencies with proper injection and resolution.

## Documentation:

Refer to the [documentation](DOCUMENTATION.md "documentation") for implementation details, examples, and instructions on how to use the service locator pattern in your TypeScript projects.
