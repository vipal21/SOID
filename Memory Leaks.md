# Finding and Fixing Memory Leaks in Swift

Memory leaks in Swift occur when objects are not properly deallocated, leading to unnecessary memory usage. This guide provides tools, techniques, and best practices to find and fix memory leaks in Swift applications.

---

## 1. Use Xcode Instruments: Leaks Tool
The **Leaks** instrument helps detect memory leaks during runtime.

### Steps:
1. **Open Instruments**:  
   - In Xcode, go to **Product > Profile** or press `Cmd + I`.
2. **Select Leaks**:  
   - Choose the **Leaks** template from the Instruments list.
3. **Run the App**:  
   - Interact with your app, focusing on areas where leaks might occur.
4. **Analyze Results**:  
   - Instruments will highlight leaks and show objects that are not deallocated.

---

## 2. Debug Memory Graph
The Memory Graph Debugger in Xcode provides a snapshot of your app's memory usage.

### Steps:
1. **Run your App**:  
   - Run the app in the simulator or on a device using `Cmd + R`.
2. **Trigger the Memory Graph**:  
   - Click the **Debug Memory Graph** button in the Debug Navigator (icon: small rectangle with a circle).
3. **Inspect Objects**:  
   - Look for objects that remain in memory longer than expected. Cyclic references are often highlighted.

---

## 3. Look for Retain Cycles
A retain cycle occurs when two objects hold strong references to each other, preventing deallocation.

### Examples:
- **ViewController and Closures**:  
   - Closures capturing `self` can create retain cycles.
   
- **Parent-Child Relationships**:  
   - Strong references in both directions between objects.

### How to Fix:
- Use **weak** or **unowned** references where applicable.
- Use `[weak self]` or `[unowned self]` inside closures.

---

## 4. Analyze Using Logs
Add deinitialization logs to detect objects that should be deallocated but aren’t.

### Example:
```swift
class ViewController: UIViewController {
    deinit {
        print("ViewController is deallocated")
    }
}
