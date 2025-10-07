---
title: Overview
project: wraith
---

# Wraith

High-performance Java event handling library designed for developers seeking a
customizable and efficient event-driven architecture. It provides a
straightforward API to define events, listeners, and event buses, facilitating
decoupled communication within applications.

## Core Components

### Event

An event is a Java class representing a specific occurrence or action within the
application. There are no strict requirements for event classes; any
object can serve as an event.

```java
public class CustomEvent {

    private final String message;

    public CustomEvent(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }
}
```

### Listener

Listeners handle events and can be defined by extending `EventListener<T>` or
using `LambdaEventListener<T>`. They are associated with a `Target` to specify
which events they should respond to.

```java
public class MyListener extends EventListener<MyEvent> {

    public MyListener() {
        super(Target.fine(MyEvent.class));
    }

    @Override
    public void invoke(MyEvent event) {
        // Handle the event
    }
}
```

Alternatively, using a lambda expression:

<!--@formatter:off -->
```java
new LambdaEventListener<>(
    Target.fine(MyEvent.class),
    event -> {
        // Handle the event
    }
);
```
<!--@formatter:on -->

### Target

Targets define the criteria for listener invocation and event dispatching. They
provide fine-grained control over which listeners respond to which events.

- `Target.all()`: Matches all classes.
- `Target.fine(Class<?>)`: Matches the specified class exactly.
- `Target.cascade(Class<?>)`: Matches the specified class and all its subclasses.



### Subscriber

Subscribers are classes that register one or more listeners with an event bus.
They can extend the `Subscriber` class or implement the `ISubscriber` interface.

```java
public class CustomSubscriber extends Subscriber {

    public CustomSubscriber() {
        registerListener(new MyListener());
    }
}
```

### EventBus

The `EventBus` manages the registration of subscribers and the dispatching of
events. It ensures that events are delivered to all appropriate listeners.

<!-- @formatter:off -->
```java
IEventBus eventBus = new EventBus();
eventBus.subscribe(new CustomSubscriber());
eventBus.dispatch(new CustomEvent("Hello, Wraith!"));
```
<!-- @formatter:on -->