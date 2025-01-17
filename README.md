# Tutorial 10
Tutorial 1: Timer
This tutorial is based on this part of the book:
https://rust-lang.github.io/async-book/02_execution/04_executor.html

## Experiment 1.2: Understanding how it works
![Experiment 1.2 Screen Capture](assets/images/exp1-2.png)

After adding the line `println!("Amzu's Computer: hey hey")` to the code, it displays the "hey hey" line first before anything else. Lets break it down then.. So in the program, a task is spawned using `spawner.spawn(async {...})`. This task consists of asynchronous code to print "howdy!" and then waits for the timer future to complete. However, the task is not executed immediately, it is queued for execution by the executor.

After spawning the task, the synchronous code in `main` function continues to execute, therefore it prints "hey hey" to the console. And then then the line `drop(spawner)` is executed, indicates to the executor that no more tasks will be added to the queue. So it runs the `executor.run()`. It executes the spawned task to prints "howdy!" and waits for the timer future to complete. Once the timer future completes after the specified duration, the task prints "done!". Basically, "hey hey" is printed synchonously before any of the asynchronous tasks are executed because it's part of the main function's execution workflow.

## Experiment 1.3: Multiple Spawn and removing drop
### Multiple Spawn
![Experiment 1.3 Multiple Spawn](assets/images/exp1-3-multi-spawn.PNG)

By adding more `spawner.spawn({ ... })` on to the `main()` function, each call would enqueue additional tasks onto the task queue managed by the executor. These tasks would be executed in the order they were spawned, after any previously spawned tasks have been **processed**.

### Removing Drop
![Experiment 1.3 Removing Drop](assets/images/exp1-3-removing-drop.png)

Removing the line `drop(spawner)` from the `main()` function, it means that the executor wouldn't receive the signal indicating that no more tasks will be added to the task queue. As a result, **the executor would continue to wait indefinitely for new tasks to arrive**, even though there are no more tasks being added. This leads to the program never terminating, as the executor would keep waiting for tasks that will never come.
