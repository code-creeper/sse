# Plan: Basic SSE Setup (Micronaut Java)

## Context

This implements the minimal SSE endpoint so a frontend app can open a persistent connection and receive events every 3 seconds. No business logic — purely the raw SSE plumbing, in line with the monorepo's learning goal.

---

## Changes Required

### 1. `build.gradle.kts` — Add Reactor dependency

Micronaut 4 uses Project Reactor for reactive streams. SSE requires a `Publisher<Event<T>>` return type; `micronaut-reactor` bridges Reactor's `Flux` into Micronaut's reactive pipeline.

```kotlin
implementation("io.micronaut.reactor:micronaut-reactor")
```

### 2. `src/main/resources/application.properties` — Enable CORS

The frontend (different origin) needs CORS headers on the SSE endpoint.

```properties
micronaut.server.cors.enabled=true
micronaut.server.cors.configurations.web.allowed-origins=*
micronaut.server.cors.configurations.web.allowed-methods=GET
```

### 3. `src/main/java/mn/java/SseController.java` — New file

Returns a `Flux<Event<String>>` that emits one event every 3 seconds. Micronaut serializes this as `text/event-stream` automatically.

```java
package mn.java;

import io.micronaut.http.MediaType;
import io.micronaut.http.annotation.Controller;
import io.micronaut.http.annotation.Get;
import io.micronaut.http.sse.Event;
import reactor.core.publisher.Flux;
import java.time.Duration;
import java.time.Instant;

@Controller("/sse")
public class SseController {

    @Get(value = "/stream", produces = MediaType.TEXT_EVENT_STREAM)
    public Flux<Event<String>> stream() {
        return Flux.interval(Duration.ofSeconds(3))
                .map(tick -> Event.of("tick:" + tick + " at " + Instant.now())
                        .name("message")
                        .id(String.valueOf(tick)));
    }
}
```

- `Event.of(data)` — wraps payload as SSE `data:` field
- `.name("message")` — sets SSE `event:` field (frontend listens with `addEventListener("message", ...)`)
- `.id(tick)` — sets SSE `id:` field (allows reconnect resume via `Last-Event-ID` header)

---

## Files to Create / Modify

| Action | Path |
|--------|------|
| Modify | `build.gradle.kts` |
| Modify | `src/main/resources/application.properties` |
| Create | `src/main/java/mn/java/SseController.java` |

---

## Verification

1. `./gradlew run` — server starts on `http://localhost:8080`
2. `curl -N http://localhost:8080/sse/stream` — should print an SSE event every 3 seconds:
   ```
   id:0
   event:message
   data:tick:0 at 2026-05-03T...

   id:1
   event:message
   data:tick:1 at 2026-05-03T...
   ```
3. `./gradlew test` — existing `MnJavaTest` must still pass
