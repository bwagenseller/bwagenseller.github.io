# Akka Classic: Rehydration and WakeUp

> Concept: [Rehydration and WakeUp](/learn_to_code/java/akka/concepts/rehydration_and_wakeup). This page is the **Classic API** implementation.  
> <font color="red">STUB - to be filled in.</font>  

# preStart Broadcast

```java
@Override
public void preStart() {
    // runs on every new instance, INCLUDING the one created after failover
    for (String printerId : configuredPrinters) {
        printerShardRegion.tell(new WakeUp(printerId), getSelf());
    }
    getTimers().startTimerAtFixedRate("liveness", LIVENESS_TICK, livenessInterval);
}
```

# Idempotent WakeUp In The Entity

```java
private void onWakeUp(WakeUp msg) {
    if (!poweredOn) { poweredOn = true; register(); }        // first WakeUp
    else { register(); jobs.forEach(this::publishJob); }     // re-WakeUp -> rehydrate
}
```

# TODO

- Liveness self-heal: re-WakeUp only unregistered entities.  
- Stale-deregister guard.  
- Worked code from the print-shop example.  
