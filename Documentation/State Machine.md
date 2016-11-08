# State machine

Task lifetime is defined through it's state of execution and the task state is exposed as a [`state`](https://github.com/arikis/Overdrive/blob/master/Sources/Overdrive/TaskBase.swift#L35) property.

From the framework user perspective, state can't be changed manually. It can be changed during the execution by the `TaskQueue` or it can be changed by the user (for ex. calling a `finish(with:)` method).

Any task state change is done in the thread-safe manner, meaning that you can call any state changing method from any thread and be sure that the task will perform like expected.

At any point in time, task state can be:

* [Initialized](#initialized)
* [Pending](#pending)
* [Ready](#ready)
* [Executing](#executing)
* [Finished](#finished)

## Initialized

Every task starts of with the `initialized` state. At this point, you can configure task observers, conditions and dependencies.

## Pending

Once the task is added to the `TaskQueue` instance, its state changes to `pending` at it is scheduled for execution.

## Ready

Once the task changes state to `pending`, complex readiness evaluation begins. This is the most important step before the task is actually executed. Any task subclass can define is own  way of defining when it's ready for execution, but the default implementation checks if all task conditions are satisfied and if all dependencies are finished with work.

## Executing

After task reaches `ready` state, task execution begins. `TaskQueue` doesn't have any control on how long this process takes since tasks can be asynchronous. To finish execution, you must call `finish(with: )` method.

## Finished

After the `finish(with:)` method is called task changes state to `finished`. Any configured completion blocks like `onValue:` and `onError:` are executed and task is removed from the `TaskQueue`.

---

<img src="http://i.imgur.com/4nrdY5g.png" width="50%" height="50%" />
> Detailed graph of task state machine.