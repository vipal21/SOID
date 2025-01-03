## 📚 Therad Overview

### **1.  What is thread?**
A thread is a sequence of instructions that a program can execute independently.
 
 **Example**
     
  ```swift
  import Foundation
  
  // Function to simulate a time-consuming task
  func performTask(taskName: String) {
      for i in 1...3 {
          print("\(taskName) is working on step \(i)")
          Thread.sleep(forTimeInterval: 1) // Simulate a delay of 1 second
      }
      print("\(taskName) is done")
  }
  
  // Creating a thread to run Task 1
  let thread1 = Thread {
      performTask(taskName: "Task 1")
  }
  
  // Creating another thread to run Task 2
  let thread2 = Thread {
      performTask(taskName: "Task 2")
  }
  
  // Start both threads
  thread1.start()
  thread2.start()
  
  // The main thread continues its work
  print("Main thread is free to do other things!")
  ```
  ### **Explanation**
  1. Task 1 and Task 2 run on separate threads. 
  2. Each thread works independently, so Task 1 doesn’t have to wait for Task 2 to finish, and vice versa. 
  3. The main thread is free to execute other tasks (e.g., updating the UI or printing "Main thread is free").
  ### **Output**
  ```swift
  Main thread is free to do other things!
  Task 1 is working on step 1
  Task 2 is working on step 1
  Task 1 is working on step 2
  Task 2 is working on step 2
  Task 1 is working on step 3
  Task 2 is working on step 3
  Task 1 is done
  Task 2 is done
  ```
 This approach demonstrates how the **SOLID** principles promote clean, maintainable, and flexible code.
 
### **2. Why we need GCD?**

  We need Grand Central Dispatch (GCD) to simplify and optimize thread management.Manually creating and managing threads is complex, error-prone. 
  GCD provides an easy-to-use, high-level API for handling concurrency efficiently.
  1.   **Thread Pool Management**
        - GCD manages a pool of threads for you, reusing them to save resources
        - You don’t need to create or destroy threads manually.
  2.   **Performance Optimization**
       - It automatically adjusts the number of active threads based on the current system state
  3.   **Ease of Use**
       - With GCD, you don’t have to deal with low-level threading APIs.
  4.   **Avoids Common Pitfalls**
       - GCD helps avoid threading issues like deadlocks and race conditions by offering serial queues
  5.   **Concurrency Models**
       - Supports both serial execution (tasks run one after another) and concurrent execution (tasks run in parallel)
        ```swift
        // Serial queue
          let serialQueue = DispatchQueue(label: "com.example.serial")
          serialQueue.async { print("Task 1") }
          serialQueue.async { print("Task 2") }
          
          // Concurrent queue
          DispatchQueue.global().async { print("Task A") }
          DispatchQueue.global().async { print("Task B") }
        ``` 
  6.   **Integration with Main Thread**
       - Simplifies switching between background threads and the main thread (essential for UI updates)
         ```swift
               DispatchQueue.global().async {
                  let result = heavyComputation()
                  DispatchQueue.main.async {
                  print("Update UI with result: \(result)")
                  }
                }
         ```
 
### **3. Types of queues in GCD**

In Grand Central Dispatch (GCD), queues are used to manage tasks and determine how those tasks are executed.

  1.   **Serial Queues**
       -  A serial queue executes tasks one after another, in the order they are added.
       -  Even if multiple tasks are added to a serial queue, they will run sequentially, one at a time.
       -  Good for tasks that should not run concurrently, such as updating shared resources or performing I/O operations
        ```swift
        // Serial queue
        let serialQueue = DispatchQueue(label: "com.example.serialQueue")
        
        serialQueue.async {
            print("Task 1")
        }
        
        serialQueue.async {
            print("Task 2")
        }
        ```
        **Output:**
        ```swift
        Task 1
        Task 2
        ```
  2.   **Concurrent Queues**
       - A concurrent queue can execute multiple tasks in parallel, meaning tasks can start before previous ones are finished.
       - Tasks added to a concurrent queue run concurrently (in parallel), but they may not necessarily complete in the same order they were added.
       - Ideal for tasks that can be performed simultaneously, like downloading files or processing data in chunks.
        ```swift
        let concurrentQueue = DispatchQueue.global(qos: .background)
        
        concurrentQueue.async {
            print("Task A")
        }
        
        concurrentQueue.async {
            print("Task B")
        }

        ```
        **Output:**
        ```swift
        Task A
        Task B
        ```
  3.   **Main Queue**
       - The main queue is a serial queue that runs tasks on the main thread of your app, which is used for updating the UI.
       - Tasks added to the main queue execute on the main thread and synchronously block the main thread until they finish (unless they are executed asynchronously).
       - Used for UI updates or any tasks that must run on the main thread.
        ```swift
            DispatchQueue.main.async {
            print("This runs on the main thread (UI updates).")
                }
        ```
  4.   **Global Queues**
       - These are concurrent queues provided by the system, with different Quality of Service (QoS) levels. They are ideal for background tasks that can run concurrently.
       - Global queues allow you to specify the priority for tasks
       - Used for background processing tasks that don’t need to run on the main thread.
        ```swift
            DispatchQueue.global(qos: .userInitiated).async {
                print("High-priority task in background.")
            }
        ```
        The available QoS levels for global queues are:
         - **userInteractive:** Tasks that need to be completed immediately (e.g., UI updates).
         - **userInitiated:** Tasks that the user is actively waiting for.
         - **utility:** Tasks that can run in the background but still require a timely response.
         - **background:**  Long-running tasks that can run in the background without affecting the user experience
        
         ### **Summary of GCD Queues**
         - **Serial Queue:** Executes tasks one at a time, in the order they are added.
         - **Concurrent Queue:** Executes multiple tasks at the same time (tasks may finish in any order).
         - **Main Queue:** A serial queue that executes tasks on the main thread, mainly for UI updates.
         - **Global Queues:** Predefined concurrent queues with different priorities, provided by the system for background tasks.
  
  ### **4. What is Sync and Asyc in GCD ?**
  
  In Grand Central Dispatch (GCD), async and sync are two key concepts that define how tasks are executed in relation to other tasks.

  1.   **async (Asynchronous)**
       -  When you dispatch a task asynchronously, it means the task runs in the background and does not block the current thread. The current thread continues its work, and the task you dispatched will execute when a thread is available.
        ```swift
            DispatchQueue.global().async {
            print("This task runs asynchronously in the background.")
            }
            print("This is printed immediately, without waiting for the task to finish.")

        ```
  2.   **sync (Synchronous)**
       -  When you dispatch a task synchronously, the task runs on the current thread and blocks the current thread until the task completes. The program will not move on to the next line of code until the task finishes.
        ```swift
            DispatchQueue.global().sync {
                print("This task runs synchronously in the background and blocks the current thread.")
            }
            print("This is printed after the task finishes because the thread was blocked.")
        ```
        **Output:**
        ```swift
        Task 1
        Task 2
        ```
  ### **4. Deadlock Example**

   All possible combinations of `async` and `sync` calls within a **single serial queue** using Grand Central Dispatch (GCD) in Swift, with explanations and examples of whether a deadlock occurs.
  
  | **Outer Task** | **Inner Task** | **Combination**                               | **Deadlock?** | **Explanation**                                                                                   |
  |-----------------|----------------|-----------------------------------------------|---------------|---------------------------------------------------------------------------------------------------|
  | `async`         | `async`        | Outer task is async, inner task is async      | ❌ No         | Both tasks are added to the queue and execute sequentially. Neither task blocks the queue.       |
  | `async`         | `sync`         | Outer task is async, inner task is sync       | ❌ No         | Outer task starts, inner task blocks only the outer task, and completes before outer continues.  |
  | `sync`          | `async`        | Outer task is sync, inner task is async       | ❌ No         | Outer task starts and waits for the queue to finish executing inner async task. No circular wait.|
  | `sync`          | `sync`         | Outer task is sync, inner task is sync        | ✅ Yes        | Deadlock occurs because outer sync blocks the queue, but it also waits for the inner sync task. |
  
  
  ## Key Points to Remember:
  
  ### `async` + `async`
  - **Behavior**: Both tasks are added to the queue independently, so no blocking occurs.
  - **Reason**: Async tasks do not wait for each other, so the queue processes them sequentially.
  
  ### `async` + `sync`
  - **Behavior**: The outer async starts and waits for the inner sync to complete.
  - **Reason**: No deadlock because the queue isn't already blocked when the sync task is added.
  
  ### `sync` + `async`
  - **Behavior**: The outer sync waits for the queue to complete the inner async task.
  - **Reason**: Since async doesn't block, the tasks execute sequentially without issue.
  
  ### `sync` + `sync`
  - **Behavior**: Deadlock happens because the outer sync blocks the queue while also waiting for the inner sync to complete.
  - **Reason**: Circular wait condition arises, causing a freeze.
  
  ## Examples:
  
  ```swift
  let serialQueue = DispatchQueue(label: "serialQueue")
  
  serialQueue.sync {
      print("Outer Task Started")
  
      serialQueue.sync {
          print("Inner Task (sync) Running")
      }
  
      print("Outer Task Finished")
  }
  ```
  
  **Output**:
  - The program freezes (deadlock occurs).
  
  **Explanation**: The outer sync blocks the queue while waiting for the inner sync to complete. However, the inner sync cannot run because the queue is still busy with the outer sync, causing a circular wait.
  
### **5.  What is Dispatch Group?**
`DispatchGroup` is a powerful tool in Grand Central Dispatch (GCD) that helps synchronize and track the completion of multiple asynchronous tasks.

## Features
- **Track multiple tasks**: Add tasks to a `DispatchGroup` and be notified when all tasks complete.
- **Synchronize operations**: Block execution until all tasks in the group finish (if needed).
- **Timeout handling**: Specify a timeout to prevent indefinite waiting.
  
## Key Methods

| **Method**                  | **Description**                                                                                                     |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------|
| `enter()`                   | Indicates that a task has started and is being tracked by the group.                                                |
| `leave()`                   | Indicates that a task has completed. Must always match a preceding `enter()` call.                                  |
| `notify(queue:execute:)`    | Executes a closure on the specified queue after all tasks in the group have completed.                              |
| `wait()`                    | Blocks the current thread until all tasks in the group are complete.                                                |
| `wait(timeout:)`            | Blocks the current thread until all tasks in the group are complete or the specified timeout expires.               |

 **Example**
     
  ```swift
  ```swift
import Foundation

// Create a dispatch group
let dispatchGroup = DispatchGroup()

// Define a concurrent queue
let queue = DispatchQueue.global()

// Task 1: Add a task to the group
dispatchGroup.enter()
queue.async {
    print("Task 1 started")
    sleep(2) // Simulate a 2-second operation
    print("Task 1 completed")
    dispatchGroup.leave()
}

// Task 2: Add another task to the group
dispatchGroup.enter()
queue.async {
    print("Task 2 started")
    sleep(1) // Simulate a 1-second operation
    print("Task 2 completed")
    dispatchGroup.leave()
}

// Wait for all tasks to complete
print("Waiting for tasks to complete...")
dispatchGroup.wait() // Blocks the current thread until Task 1 and Task 2 finish
print("All tasks completed, proceeding to the next step")

// Task 3: Automatically tracked task
queue.async(group: dispatchGroup) {
    print("Task 3 started")
    sleep(1) // Simulate another 1-second operation
    print("Task 3 completed")
}

// Notify when all tasks are done
dispatchGroup.notify(queue: DispatchQueue.main) {
    print("All tasks, including Task 3, are now complete")
}

// Allow the program to run until all tasks finish
sleep(5)
  ``` 
 **Output**
     
  ```swift
  Waiting for tasks to complete...
  Task 1 started
  Task 2 started
  Task 2 completed
  Task 1 completed
  All tasks completed, proceeding to the next step
  Task 3 started
  Task 3 completed
  All tasks, including Task 3, are now complete

  ``` 
