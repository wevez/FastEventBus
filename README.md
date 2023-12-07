# FastEventBus
An EventBus with no try-catch blocks.  
Try-catch blocks are one of the causes of performance degradation, but for some reason other EventBus use try-catch blocks when executing events. 
This is why I have created this repository.

Try-catch ブロックはパフォーマンス低下の原因の 1 つですが、何らかの理由で他の EventBus はイベントの実行時に Try-catch ブロックを使用します。これがこのリポジトリを作成した理由です。
## How to use
- Make a custom EventArgument
```java
public class MoveEvent extends EventArgument {

    public double x, y, z;

    public MoveEvent(double x, double y, double z) {
        super();
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public void call(final EventListener listener) {
        listener.onMove(this);
    }
}
```
- Add an event calling function corresponding to the custom EventArgument in the EventListener  
```java
package jp.uuum.eventbus;

public interface EventListener {

    default void onMove(final MoveEvent event);
}

```
- Make a event listener class implements EventListener.
```java
public class TestListener implements EventListener {

    @Override
    public void onMove(final MoveEvent event) {
        System.out.printf("x: %f, y: %f, z: %f\n", event.x, event.y, event.z);
        
        if (event.x == 0) {
            event.cancel();
        }
    }
}
```
- Let's test it!
```java
public class Main {

    public static void main(final String[] args) {
        // Create an instance of EventManager
        final EventManager MANAGER = new EventManager();

        // Create an instance of TestListener and register it to the instance of EventManager
        final TestListener TEST_LISTENER = new TestListener();
        MANAGER.register(TEST_LISTENER);
        
        // Call event using MoveEvent with arguments x: 0, y: 0 z: 0
        MoveEvent moveEvent = new MoveEvent(0, 0, 0);
        TEST_LISTENER.call(moveEvent);

        System.out.println(moveEvent.isCanceled() ? "moveEvent is canceled" : "moveEvent is not canceled");

        // Unregister the TEST_LISTENER from MANAGER
        MANAGER.remove(TEST_LISTENER);
    }
}
```