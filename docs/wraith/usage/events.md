# Events

## Definition

Any object can be used as an event, but it's good practice to use standardized
event classes. Events can be small, large, simple, or very complex. Anything
goes!

A few example event classes:

```java
public class EmptyEvent {

}
```

```java
public class MessageEvent {

    private String message;

    public MessageEvent(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
```

```java
public class SunEvent {

    private final String description;

    public SunEvent(String description) {
        this.description = description;
    }

    public String getDescription() {
        return message;
    }

    public static class SunriseEvent extends SunEvent {

        public SunriseEvent() {
            super("The sun rises");
        }
    }

    public static class SunsetEvent extends SunEvent {

        public SunsetEvent() {
            super("The sun sets");
        }
    }
}
```

## Dispatching

To dispatch an event, call one of the `dispatch` methods defined in `EventBus`,
passing your event as a parameter.

``` java
MessageEvent event = new MessageEvent("world greetings");

EVENT_BUS.dispatch(event);
```

## Status Event

A status event can be suppressed or terminated, and the `StatusEvent` class and
`IStatusEvent` interface provide methods to manipulate and check their status.

### Status Definitions

Possible statuses are defined by the `IStatusEvent.EventStatus` enum.

| Value      | Description                                                          |
|------------|----------------------------------------------------------------------|
| ALIVE      | The event is active and not suppressed or terminated.                |
| SUPPRESSED | The event is suppressed. A suppressed event may become unsuppressed. |
| TERMINATED | The event is terminated and cannot be altered further.               |

### Event Bus Interaction

When a Status Event is dispatched, the dispatch method will return `true` if
the event was suppressed or terminated by any listener.

``` java
if (eventBus.dispatch(new StatusEvent())) {
    System.out.println("Event was suppressed or terminated");
} else {
    System.out.println("Event is still alive");
}
```

### Defining a Status Event

=== "StatusEvent"

    Extend the `StatusEvent` class.
    ``` java
    public class Event extends StatusEvent {
        
    }
    ```
    Completely functional out of the box, this is the recommended approach.

=== "IStatusEvent"

    If you need custom functionality, implement the `IStatusEvent` interface.
    ``` java
    public class Event implements IStatusEvent {
        
        private EventStatus status;
        
        @NotNull
        @Override
        public EventStatus getEventStatus() {
            return status;
        }

        @Override
        public void setEventStatus(@NotNull EventStatus status) {
            this.status = status;
        }

        @Override
        public boolean isSuppressed() {
            return status == EventStatus.SUPPRESSED;
        }

        @Override
        public boolean isTerminated() {
            return status == EventStatus.TERMINATED;
        }
    }
    ```

    ???+ warning
        It's not recommended to call `#!java setEventStatus()` outside of the 
        status event itself, even in your own implementations.
