## 📚 Thread Overview

### **1. Why Use NSOperation: Key Advantages**

#### **Comparison Table**
| **Feature**             | **Why Use NSOperation?**                                                                                                   | **Example**                                                                                                         |
|-------------------------|---------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| **Dependencies**         | Allows setting dependencies between tasks, ensuring one task completes before another starts.                              | Use `addDependency()` to link tasks.                                                                                |
| **Cancelation**          | Built-in cancelation mechanism lets you stop tasks gracefully when they are no longer needed.                              | Call `cancel()` on the operation to stop execution cleanly.                                                         |
| **Prioritization**       | Assign priorities to tasks using `queuePriority`, ensuring critical tasks execute first.                                     | Set `queuePriority` to `.high`, `.normal`, or `.low` for operations.                                                |
| **Progress Tracking**    | Supports tracking task progress using Key-Value Observing (KVO), ideal for reporting task completion to the user.           | Observe changes to a custom progress property in a subclassed operation.                                            |
| **Reusability**          | Encapsulate reusable tasks into custom `Operation` subclasses for modular and maintainable code.                           | Create an `Operation` subclass for tasks like downloads, data processing, or uploads.                              |
| **Pause and Resume**     | Use custom logic in `NSOperation` subclasses to pause and resume tasks flexibly during execution.                          | Implement `pause()` and `resume()` using locks or custom flags to manage task state.                                |

---

#### **1. Dependencies**
- **Why?** NSOperation allows you to specify dependencies between tasks easily. This ensures that one task does not start until another has finished.

#### **Example**
```swift
let downloadOperation = BlockOperation {
    print("Downloading image...")
    sleep(2)
    print("Image downloaded")
}

let processOperation = BlockOperation {
    print("Processing image...")
    sleep(1)
    print("Image processed")
}

let updateUIOperation = BlockOperation {
    print("Updating UI with processed image")
}

// Setting dependencies
processOperation.addDependency(downloadOperation)
updateUIOperation.addDependency(processOperation)

let queue = OperationQueue()
queue.addOperations([downloadOperation, processOperation, updateUIOperation], waitUntilFinished: false)
```

---

#### **2. Cancelation**
- **Why?** NSOperation provides built-in cancelation support, allowing you to stop tasks cleanly if they are no longer needed.

#### **Example**
```swift
let operation = BlockOperation {
    guard !operation.isCancelled else { return }
    print("Executing task...")
    sleep(2)
    print("Task completed")
}

operation.cancel() // Cancel the operation before it starts

let queue = OperationQueue()
queue.addOperation(operation)
```

---

#### **3. Prioritization**
- **Why?** Tasks can be prioritized using `queuePriority`, helping you execute critical tasks first.

#### **Example**
```swift
let lowPriorityOperation = BlockOperation { print("Low-priority task") }
lowPriorityOperation.queuePriority = .low

let highPriorityOperation = BlockOperation { print("High-priority task") }
highPriorityOperation.queuePriority = .high

let queue = OperationQueue()
queue.addOperations([lowPriorityOperation, highPriorityOperation], waitUntilFinished: false)
```

---

#### **4. Progress Tracking**
- **Why?** NSOperation supports progress reporting using Key-Value Observing (KVO).

#### **Example**
```swift
class ProgressOperation: Operation {
    private(set) var progress: Double = 0

    override func main() {
        for i in 1...10 {
            if isCancelled { return }
            progress = Double(i) / 10.0
            print("Progress: \(progress * 100)%")
            sleep(1)
        }
        print("Task completed")
    }
}

let progressOperation = ProgressOperation()
let queue = OperationQueue()
queue.addOperation(progressOperation)
```

---

#### **5. Reusability**
- **Why?** NSOperation subclasses allow you to encapsulate reusable tasks.

#### **Example**
```swift
class DownloadOperation: Operation {
    override func main() {
        print("Downloading file...")
        sleep(2)
        print("File downloaded")
    }
}

let downloadTask = DownloadOperation()
let queue = OperationQueue()
queue.addOperation(downloadTask)
```

---

#### **6. Pause and Resume**
- **Why?** NSOperation allows pausing and resuming tasks by utilizing `isCancelled` and custom flags.

#### **Example**
```swift
class PauseableOperation: Operation {
    private var isPaused = false
    private let lock = NSLock()

    func pause() {
        lock.lock()
        isPaused = true
        lock.unlock()
    }

    func resume() {
        lock.lock()
        isPaused = false
        lock.unlock()
    }

    override func main() {
        for i in 1...10 {
            if isCancelled { return }

            lock.lock()
            while isPaused {
                lock.unlock()
                Thread.sleep(forTimeInterval: 0.1)
                lock.lock()
            }
            lock.unlock()

            print("Step \(i) completed")
            sleep(1)
        }
        print("Operation completed")
    }
}

let pauseableOperation = PauseableOperation()
let queue = OperationQueue()
queue.addOperation(pauseableOperation)

// Simulating pause and resume
dispatch_after(DispatchTime.now() + 3) { pauseableOperation.pause() }
dispatch_after(DispatchTime.now() + 6) { pauseableOperation.resume() }
```

---

