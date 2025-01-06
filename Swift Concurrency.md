# Swift Concurrency Made Simple

Swift Concurrency, introduced in **Swift 5.5**, has transformed how developers handle asynchronous code in iOS and macOS apps. With structured concurrency, Swift makes asynchronous programming easier, safer, and more intuitive.

In this guide, we will explore the key concepts of Swift Concurrency with simple explanations and examples.

---

## Why Use Swift Concurrency?
Concurrency allows multiple tasks to run simultaneously, improving app responsiveness and performance. Traditionally, iOS developers used:
- **GCD (Grand Central Dispatch)**
- **Operation queues**
- **Callbacks, delegation, or completion handlers**

These approaches often led to complex and hard-to-maintain "callback hell."

Swift Concurrency solves these challenges with **async/await syntax** and **actors** for structured concurrency.

---

## Key Concepts in Swift Concurrency

### 1. Async/Await

The **async/await** syntax makes asynchronous programming as simple as writing synchronous code. It removes the need for nested closures or chaining.

#### Example: Fetch Weather Data
```swift
func fetchWeatherData() async throws -> String {
    let url = URL(string: "https://api.weather.com/v1")!
    let (data, _) = try await URLSession.shared.data(from: url)
    let weather = String(data: data, encoding: .utf8) ?? "No data"
    return weather
}

func displayWeather() async {
    do {
        let weather = try await fetchWeatherData()
        print("Weather: \(weather)")
    } catch {
        print("Failed to fetch weather: \(error)")
    }
}
```
- `async` marks a function as asynchronous.
- `await` pauses execution until the asynchronous task is complete.

---

### 2. Task

A **Task** represents a unit of work. You can use it to create concurrent tasks that run in parallel.

#### Example: Perform Multiple Tasks
```swift
func performTasks() async {
    async let result1 = fetchData1()
    async let result2 = fetchData2()

    let combinedResult = await (result1 + result2)
    print("Combined Result: \(combinedResult)")
}
```
- `async let` creates tasks that run in parallel.
- `await` ensures all tasks are complete before using their results.

---

### 3. Actor

**Actors** provide a safe way to handle shared, mutable state in concurrent environments. They prevent race conditions by allowing only one task to access their state at a time.

#### Example: Thread-Safe Counter
```swift
actor Counter {
    private var count = 0

    func increment() {
        count += 1
    }

    func getCount() -> Int {
        count
    }
}

let counter = Counter()

Task {
    await counter.increment()
    let currentCount = await counter.getCount()
    print("Count: \(currentCount)")
}
```
- `actor` ensures thread-safe access to its state without requiring manual synchronization.

---

### 4. TaskGroup

**Task groups** allow you to create and manage dynamic collections of tasks, processing their results as they complete.

#### Example: Perform Batch Tasks
```swift
func performBatchTasks() async {
    await withTaskGroup(of: String.self) { group in
        group.addTask { await fetchData1() }
        group.addTask { await fetchData2() }

        for await result in group {
            print("Task result: \(result)")
        }
    }
}
```
- Task groups are great for running multiple tasks in parallel and processing their results dynamically.

---

### 5. Structured vs. Unstructured Concurrency

#### Structured Concurrency
In structured concurrency, tasks are created within a defined scope. The lifetime of tasks is tied to the scope, ensuring proper cleanup and error handling.

#### Example: Structured Concurrency
```swift
func fetchUserData() async {
    await withTaskGroup(of: String.self) { group in
        group.addTask { "Name: John" }
        group.addTask { "Age: 30" }

        for await detail in group {
            print(detail)
        }
    }
}
```
- Tasks are created within a `withTaskGroup` block.
- Ensures tasks complete before exiting the scope.

#### Unstructured Concurrency
Unstructured concurrency creates tasks without a defined scope, giving more control but requiring explicit management.

#### Example: Unstructured Concurrency
```swift
let task = Task {
    return await fetchData()
}

Task {
    let result = await task.value
    print("Fetched Data: \(result)")
}
```
- Tasks are created explicitly using `Task`.
- Requires manual management of task lifecycle.

**Key Difference**:
- **Structured Concurrency** ensures tasks complete within a defined scope, making it safer and easier to manage.
- **Unstructured Concurrency** provides more flexibility but requires careful handling to avoid resource leaks.

---

### 6. Task Priority

**Task priorities** allow you to specify the importance of tasks, helping the system prioritize resource allocation.

#### Example: Setting Task Priority
```swift
Task(priority: .high) {
    let data = await fetchData()
    print("High-priority task fetched: \(data)")
}

Task(priority: .low) {
    let data = await fetchOtherData()
    print("Low-priority task fetched: \(data)")
}
```
- `.high`, `.medium`, `.low`, and `.background` are common task priorities.
- High-priority tasks are executed before lower-priority ones.

---

### 7. Continuations

Continuations bridge the gap between Swift Concurrency and callback-based APIs, enabling the use of `async/await` with legacy code.

#### Example: Using Continuations
```swift
func fetchDataWithContinuation() async -> String {
    return await withCheckedContinuation { continuation in
        legacyFetchData { result in
            continuation.resume(returning: result)
        }
    }
}
```
- `withCheckedContinuation` ensures safe integration with non-concurrent APIs.
- The `resume(returning:)` method signals the continuation to proceed.

#### Example: Handling Errors with Continuations
```swift
func fetchDataWithError() async throws -> String {
    return try await withCheckedThrowingContinuation { continuation in
        legacyFetchDataWithError { result, error in
            if let error = error {
                continuation.resume(throwing: error)
            } else {
                continuation.resume(returning: result!)
            }
        }
    }
}
```
- Use `withCheckedThrowingContinuation` to handle errors.
- Resumes with `returning` or `throwing` based on API results.

---

### 8. Global Actors

**Global actors** provide a way to group tasks that need to be executed in a specific context, such as the main thread.

#### Example: Using the MainActor
The `@MainActor` attribute ensures that tasks run on the main thread. This is particularly useful for UI updates, which must always be performed on the main thread.

```swift
@MainActor
class UIManager {
    func updateUI() {
        print("Updating UI on the main thread")
    }
}

Task {
    let manager = UIManager()
    await manager.updateUI()
}
```
Explanation:
1. **`@MainActor`**: Ensures that the `updateUI` function always runs on the main thread.
2. **Use Case**: Ideal for performing UI-related tasks safely without manually managing threads.

#### Example: Custom Global Actor
A custom global actor allows you to group tasks for specific functionality, ensuring thread safety for tasks within the actor.

```swift
@globalActor
actor NetworkActor {
    static let shared = NetworkActor()

    func fetchData() async {
        print("Fetching data in a network-safe context")
    }
}

Task { @NetworkActor in
    await NetworkActor.shared.fetchData()
}
```
Explanation:
1. **`@globalActor`**: Declares a custom global actor for grouping tasks.
2. **Shared Instance**: The `shared` property provides a single, thread-safe entry point.
3. **Use Case**: Ensures all tasks related to network operations are executed in a safe, controlled environment.

---

### 9. Sendable

**Sendable** ensures that data passed between concurrent tasks is safe to use. It guarantees immutability or thread-safe access.

#### Example: Using Sendable
```swift
struct User: Sendable {
    let id: Int
    let name: String
}

actor UserManager {
    private var users: [User] = []

    func addUser(_ user: User) {
        users.append(user)
    }

    func getUsers() -> [User] {
        return users
    }
}

Task {
    let user = User(id: 1, name: "Alice")
    let manager = UserManager()

    await manager.addUser(user)
    let allUsers = await manager.getUsers()
    print(allUsers)
}
```
- `Sendable` ensures that `User` can safely be shared across tasks.
- Structs and classes must conform to `Sendable` explicitly if required.

---

## Conclusion
Swift Concurrency simplifies asynchronous programming with:
- **Async/Await** for cleaner, more readable code.
- **Tasks** for parallel work.
- **Actors** for safe, shared state management.
- **Task Groups** for managing multiple concurrent tasks.
- **Structured Concurrency** for safer task management.
- **Task Priorities** to optimize resource allocation.
- **Continuations** to bridge callback-based APIs with modern concurrency.
- **Global Actors** to manage tasks in specific contexts.
- **Sendable** to ensure thread-safe data sharing.

By using these features, you can write responsive, maintainable, and efficient applications with ease.

---

## References
- [Swift Concurrency Documentation](https://developer.apple.com/documentation/swift/swift_concurrency)
- [WWDC 2021: Meet Swift Concurrency](https://developer.apple.com/videos/play/wwdc2021/10132/)
