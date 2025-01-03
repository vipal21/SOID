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
### **6.  What is Dispatch semaphore?**
A `DispatchSemaphore` is a concurrency control mechanism in Swift that allows for the synchronization of threads..

## Key Methods

| **Method**    | **Description**                                          | **When to Use** |
|---------------|----------------------------------------------------------|-----------------|
| **wait()**    | Decreases the semaphore count. If the count is 0, the task will wait until it's signaled. | Use when you want a task to wait for permission to run or for a resource to become available. |
| **signal()**  | Increases the semaphore count, allowing waiting tasks to proceed. | Use when a task is done and you want to let other waiting tasks run. |

**
## Key Usecases

| **Use Case**                           | **What It Does** | **Example** |
|----------------------------------------|------------------|-------------|
| **Limiting Concurrent API Calls**      | Controls how many requests can happen at the same time. | Limit the number of downloads or API calls to prevent server overload. |
| **Database Connection Pooling**        | Limits the number of database connections at once. | Ensure only a set number of people can access the database at the same time. |
| **Synchronizing Multiple Threads**     | Makes sure all tasks finish before moving on. | Wait for several tasks to finish before continuing the program. |
| **Task Dependencies (Enforcing Order)**| Makes tasks run in a specific order. | Task B waits for Task A to finish before it starts. |

 **Example:Task Dependencies**
     
  ```swift
 let semaphore = DispatchSemaphore(value: 0)  // Start with 0 so task B waits for task A
 
 func taskA() {
     DispatchQueue.global().async {
         print("Task A started")
         sleep(2)
         print("Task A completed")
         semaphore.signal()  // Signal that task A is complete
     }
 }
 
 func taskB() {
     DispatchQueue.global().async {
         semaphore.wait()  // Wait for task A to complete
         print("Task B started")
         sleep(1)
         print("Task B completed")
     }
 }
 
 taskA()
 taskB()
 
 sleep(5)

  ``` 
 **Output**
     
  ```swift
 Task A started
 Task A completed
 Task B started
 Task B completed

  ```
 **Example: Limiting concurrent downloads**
     
  ```swift
 let semaphore = DispatchSemaphore(value: 3)  // Allow only 3 concurrent downloads

func downloadFile(url: String) {
    semaphore.wait()  // Wait until a slot is available
    print("Starting download from \(url)")
    DispatchQueue.global().asyncAfter(deadline: .now() + 2) {
        print("Download complete from \(url)")
        semaphore.signal()  // Release the slot
    }
}

let urls = ["url1", "url2", "url3", "url4", "url5"]
for url in urls {
    downloadFile(url: url)
}

// Keep the main thread alive to allow async tasks to complete
sleep(10)

  ``` 
 **Output**
     
  ```swift
Starting download from url1
Starting download from url2
Starting download from url3
Download complete from url1
Starting download from url4
Download complete from url2
Starting download from url5
Download complete from url3
Download complete from url4
Download complete from url5


  ``` 
### **6.  What is Race condition?**
A ` race condition` happens when two or more parts of a program try to access or change shared data at the same time, and the final result depends on the timing of these actions. This can lead to unexpected or incorrect behavior in the program.

## When to Prefer Each Solution for Race Conditions

| **Scenario**                              | **Recommended Method**                | **Why?**                                                                                     |
|-------------------------------------------|---------------------------------------|---------------------------------------------------------------------------------------------|
| Incrementing or modifying a shared variable concurrently | `DispatchQueue` (Serial) or `actor`   | Ensures serialized access without complex synchronization code.                            |
| Managing a pool of limited resources       | `DispatchSemaphore`                   | Controls the maximum number of threads accessing the resource simultaneously.              |
| Explicit synchronization for critical sections | `NSLock`                              | Ideal for low-level control when you can carefully handle locking and unlocking.           |
| Handling modern shared state in Swift 5.5+ | `actor`                               | Provides automatic synchronization, reducing the risk of errors in concurrent programming. |


 **Example**
     
  ```swift
 import Foundation

// Class to safely increment and retrieve a counter value
class Counter {
    private var value = 0 // Shared resource (counter)
    
    // Serial queue for thread-safe operations
    private let queue = DispatchQueue(label: "com.example.counterQueue")
    
    // Lock for manual thread synchronization
    let lock = NSLock()
    
    // Semaphore with a single permit for controlling access
    private let semaphore = DispatchSemaphore(value: 1) 

    // Increment function demonstrating multiple synchronization techniques
    func increment() {
        // --------------- LOCK BASED ----------------
        // Using NSLock to safely increment the value
        lock.lock() // Acquiring the lock
        self.value += 1
        lock.unlock() // Releasing the lock

        // --------------- SERIAL DISPATCH QUEUE BASED ----------------
        // Using a serial queue to ensure value is incremented one task at a time
        queue.async {
            self.value += 1
        }

        // ------------------- SEMAPHORE -----------------
        // Using DispatchSemaphore to control access to the shared resource
        semaphore.wait() // Waiting for a permit
        self.value += 1
        semaphore.signal() // Releasing the permit
    }

    // Function to retrieve the current value safely
    func getValue() -> Int {
        // Using a serial queue to ensure the read operation is thread-safe
        queue.sync {
            value
        }
    }
}

let counter = Counter()

// Perform the increment operation concurrently across 1000 iterations
DispatchQueue.concurrentPerform(iterations: 1000) { _ in
    counter.increment()
}

// Print the final value
print(counter.getValue()) // Always 1000


  ``` 
## Explanation of Synchronization Techniques

### **NSLock**
- **How it works**:  
  Demonstrates manual locking and unlocking to ensure thread safety.
- **When to use**:  
  Useful when you need direct control over the locking mechanism.

### **Serial DispatchQueue**
- **How it works**:  
  Ensures all tasks accessing the shared resource are executed sequentially, avoiding race conditions.

### **DispatchSemaphore**
- **How it works**:  
  Controls access to the resource by allowing only one thread to proceed at a time.
  - The `wait()` method blocks until a permit is available, and `signal()` releases the permit.


