# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This is the **SSE backend** of a learning monorepo. Its sole focus is emitting Server-Sent Events and maintaining persistent streaming connections — no business logic, no auth, no persistence. Keep it minimal.

## Commands

```bash
# Run the application
./gradlew run

# Run all tests
./gradlew test

# Run a single test class
./gradlew test --tests "mn.java.MnJavaTest"
```

## Architecture

This is a **Micronaut 4.10.13** application using Java 21, Netty as the HTTP server, and JUnit 5 for tests.

- **Entry point**: `src/main/java/mn/java/Application.java` — calls `Micronaut.run()`
- **Config**: `src/main/resources/application.properties`
- **Package root**: `mn.java`

Micronaut relies on compile-time annotation processing (no runtime reflection). New controllers and services must be annotated (e.g., `@Controller`, `@Singleton`) and are picked up automatically within the `mn.java.*` package scan defined in `build.gradle.kts`.
