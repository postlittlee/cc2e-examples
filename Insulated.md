```
Locked   Coin Unlocked {alarmOff unlock}
Locked   Pass Locked   alarmOn
Unlocked Coin Unlocked thankyou
Unlocked Pass Locked   lock
```

```java
public interface TurnstileEvents {
  void coin();
  void pass();
}
```

```java
public interface TurnstileActions {
  void lock();
  void thankyou();
  void alarmOn();
  void unlock();
  void alarmOff();
}
```

```java
public abstract
class TurnstileFSM implements TurnstileActions, TurnstileEvents {
  private enum State {Locked, Unlocked}
  private enum Event {Coin, Pass}

  private State state = State.Locked;

  private void setState(State s) {
    state = s;
  }
  public void coin() {
    handleEvent(Event.Coin);
  }
  public void pass() {
    handleEvent(Event.Pass);
  }

  private void handleEvent(Event event) {
    switch (state) {
      case Locked -> {
        switch (event) {
          case Coin -> {
            setState(State.Unlocked);
            alarmOff();
            unlock();
          }
          case Pass -> {
            setState(State.Locked);
            alarmOn();
          }
        }
      }
      case Unlocked -> {
        switch (event) {
          case Coin -> {
            setState(State.Unlocked);
            thankyou();
          }
          case Pass -> {
            setState(State.Locked);
            lock();
          }
        }
      }
    }
  }
}
```
