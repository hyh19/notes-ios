
- Threads are one of several technologies that make it possible to execute multiple **code paths** concurrently inside a single application.

- Having multiple threads in an application provides two very important potential advantages:
	- Multiple threads can improve an application’s perceived responsiveness.
	- Multiple threads can improve an application’s real-time performance on multicore systems.

- Of course, threads are not a panacea for fixing an application’s performance problems. Along with the benefits offered by threads come the potential problems. Having multiple paths of execution in an application can add a considerable amount of complexity to your code. Each thread has to coordinate its actions with other threads to prevent it from corrupting the application’s state information. Because threads in a single application share the same memory space, they have access to all of the same data structures. If two threads try to manipulate the same data structure at the same time, one thread might overwrite another’s changes in a way that corrupts the resulting data structure. Even with proper protections in place, you still have to watch out for compiler optimizations that introduce subtle (and not so subtle) bugs into your code.

- Another factor to consider is whether you need threads or concurrency at all. Threads solve the specific problem of how to execute multiple code paths concurrently inside the same process. There may be cases, though, where the amount of work you are doing does not warrant concurrency. Threads introduce a tremendous amount of overhead to your process, both in terms of memory consumption and CPU time. You may discover that this overhead is too great for the intended task, or that other options are easier to implement.

- Alternative technologies to threads
	- Operation objects
	- Grand Central Dispatch (GCD)
	- Idle-time notifications
	- Asynchronous functions
	- Timers
	- Separate processes

- Thread technologies
	- Cocoa threads
	- POSIX threads
	- Multiprocessing Services

- Run Loops
	- A run loop is a piece of infrastructure used to manage events arriving asynchronously on a thread. A run loop works by monitoring one or more event sources for the thread. As events arrive, the system wakes up the thread and dispatches the events to the run loop, which then dispatches them to the handlers you specify. If no events are present and ready to be handled, the run loop puts the thread to sleep.
	- You are not required to use a run loop with any threads you create but doing so can provide a better experience for the user. Run loops make it possible to create long-lived threads that use a minimal amount of resources. Because a run loop puts its thread to sleep when there is nothing to do, it eliminates the need for polling, which wastes CPU cycles and prevents the processor itself from sleeping and saving power.
	- To configure a run loop, all you have to do is launch your thread, get a reference to the run loop object, install your event handlers, and tell the run loop to run. The infrastructure provided by OS X handles the configuration of the main thread’s run loop for you automatically. If you plan to create long-lived secondary threads, however, you must configure the run loop for those threads yourself.

- Communication mechanisms
	- Direct messaging
	- Global variables, shared memory, and objects
	- Conditions
	- Run loop sources
	- Ports and sockets
	- Message queues
	- Cocoa distributed objects
